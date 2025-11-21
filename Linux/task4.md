# <span style="color: #ff5733;">_РАЗДЕЛ 4 Bash</span>
## <span style="color: #4CAF50;">_Задание 1 Скрипт мониторинга системы и управления логами</span>

**Цель:** cоздать скрипт, который выполняет мониторинг системы и сохраняет результаты в лог-файлы.
**Требования:**
1. Мониторинг ресурсов
●       Используйте команды для проверки загрузки CPU, памяти, места на диске.
●       Выводите результаты в лог-файл, разделяя STDOUT и STDERR.
2. Управление логами
●       Реализуйте ротацию логов (например, сохраняйте логи за последние 3 дня, старые удаляйте).
3. Сигналы и обработка ошибок
●       Добавьте обработку сигналов (SIGINT, SIGTERM), чтобы скрипт корректно завершался.
4. Отладка
●       Добавьте опцию для вывода отладочной информации (set -x).
**Результат:** Рабочий скрипт, который можно запустить и проверить.

```bash
#!/bin/bash  
  
# --- Настройки ---  
LOG_DIR="/var/log/system_monitor"   # Директория для логов  
LOG_FILE_PREFIX="system_monitor"    # Префикс имени лог-файла  
DAYS_TO_KEEP=3                      # Количество дней хранения логов  
  
# --- Функции ---  
  
# Функция для мониторинга ресурсов  
monitor_resources() {  
  DATE=$(date +"%Y-%m-%d %H:%M:%S")  
  echo "--- Мониторинг ресурсов - $DATE ---"  
  echo "CPU Usage:"  
  top -bn1 | grep "Cpu(s)"  
  echo "\nMemory Usage:"  
  free -m  
  echo "\nDisk Space:"  
  df -h  
  
  echo "--- Конец мониторинга ---"  
}  
  
# Функция ротации логов  
rotate_logs() {  
  echo "Выполняю ротацию логов..."  
  find "$LOG_DIR" -name "${LOG_FILE_PREFIX}_*.log" -mtime +"$DAYS_TO_KEEP" -delete  
  echo "Ротация логов завершена."  
}  
  
# Функция обработки сигналов  
handle_signal() {  
  echo "Скрипт завершается корректно..."  
  rotate_logs # Выполним ротацию перед завершением  
  exit 0  
}  
  
# --- Обработка сигналов ---  
trap handle_signal SIGINT SIGTERM  
  
# --- Проверка и создание директории для логов ---  
if [ ! -d "$LOG_DIR" ]; then  
  mkdir -p "$LOG_DIR"  
fi  
  
  
# **Обработка аргументов командной строки**  
while getopts "d" opt; do  
  case $opt in  
    d)  
      echo "Режим отладки включен."  
      set -x  
      ;;  
    \?)  
      echo "Неверная опция: -$OPTARG" >&2  
      exit 1  
      ;;  
  esac
done  
  
  
# --- Основной цикл скрипта ---  
echo "Скрипт мониторинга запущен. Логи сохраняются в $LOG_DIR"  
  
while true; do  
  TIMESTAMP=$(date +"%Y%m%d_%H%M%S")  
  LOG_FILE="$LOG_DIR/${LOG_FILE_PREFIX}_${TIMESTAMP}.log"  
  
  echo "Запись логов в файл: $LOG_FILE"  
  
  # Мониторинг ресурсов и запись в лог, разделение STDOUT и STDERR  
  {  
    monitor_resources  
  } > "${LOG_FILE}.stdout" 2>"${LOG_FILE}.stderr"  
  
  rotate_logs # Выполняем ротацию логов после каждого цикла  
  
  sleep 60 # Интервал мониторинга - 60 секунд (1 минута)  
done
```


## <span style="color: #4CAF50;">_Задание 2 Система резервного копирования и проверки целостности</span>

**Цель:** автоматизировать процесс резервного копирования файлов и проверку целостности.
**Требования:**
1. Создание резервных копий
	●       Напишите скрипт для инкрементального копирования файлов из заданной директории в архивы.
	●       Используйте tar или rsync для создания архивов.
2. Проверка целостности
	●       После создания архива выполните проверку целостности (например, с помощью md5sum).
3. Уведомления
	●       Если резервное копирование прошло успешно, добавьте запись в лог-файл.
	●       В случае ошибки перенаправьте сообщение в STDERR.
4. Управление расписанием
	●       Настройте выполнение скрипта через cron.
**Результат:** полностью автоматизированная система резервного копирования.


```bash
#!/bin/bash  
  
# Пути к директориям и файлам  
SOURCE_DIR="/home/user/scripts/2/source"       # Исходная директория  
BACKUP_DIR="/home/user/scripts/2/backup"       # Целевая директория для резервных копий  
LOG_FILE="/home/user/scripts/2/backup.log"     # Файл для логов  
SNAPSHOT_FILE="$BACKUP_DIR/snapshot.snar"  # Файл для отслеживания инкрементального копирования  
ARCHIVE_NAME="$BACKUP_DIR/backup_$(date +%Y%m%d_%H%M%S).tar.gz"  # Имя архива с временной меткой  
  
# Создаем директорию для бэкапов, если она не существует  
mkdir -p "$BACKUP_DIR"  
  
# Функция для логирования  
log_message() {  
    local message="$1"  
    local is_error="$2"  # 0 - успех, 1 - ошибка  
  
    if [ "$is_error" -eq 0 ]; then  
        echo "$(date): $message" >> "$LOG_FILE"  
    else  
        echo "$(date): $message" >&2  
    fi  
}  
  
# Функция для создания резервной копии с tar  
backup() {  
    local source="$1"  
    local archive="$2"  
    local snapshot="$3"  
  
    if tar -czf "$archive" -C "$source" --listed-incremental="$snapshot" .; then  
        return 0  # Успех  
    else  
        return 1  # Ошибка  
    fi  
}  
  
# Функция для проверки целостности архива  
check_integrity() {  
    local archive="$1"  
  
    # Извлекаем контрольную сумму архива  
    md5sum "$archive" > /tmp/archive.md5  
  
    # Проверяем архив на целостность  
    if tar -tzf "$archive" > /dev/null 2>&1; then  
        rm /tmp/archive.md5  
        return 0  # Архив в порядке  
    else  
        rm /tmp/archive.md5  
        return 1  # Архив поврежден  
    fi  
}  
  
# Основной код  
if backup "$SOURCE_DIR" "$ARCHIVE_NAME" "$SNAPSHOT_FILE"; then  
    log_message "Backup successful: $ARCHIVE_NAME" 0  
    if check_integrity "$ARCHIVE_NAME"; then  
        log_message "Integrity check passed for $ARCHIVE_NAME" 0  
    else  
        log_message "Integrity check failed for $ARCHIVE_NAME" 1  
    fi  
else  
    log_message "Backup failed" 1  
    exit 1  
fi

```





## <span style="color: #4CAF50;">_Задание 3 Анализ процессов и диагностика системы</span>
**Цель:** провести диагностику системы с помощью инструментов анализа процессов.
**Требования:**
1. Анализ ресурсов
	●       Напишите скрипт, который запускает несколько процессов с высокой нагрузкой на CPU, память и диск.
	●       Используйте top, htop и /proc для анализа.
2. Статусы процессов
	●       Создайте процессы-зомби и сироты, проанализируйте их статус.
	●       Используйте ps для вывода информации о процессах.
3. Использование strace:
	●       Выполните анализ системных вызовов для команды ls с помощью strace.
	●       Выведите в лог-файл список вызванных системных функций.
4. Оптимизация
	●       Измените приоритет одного из процессов (с помощью nice или ionice) и посмотрите, как это влияет на производительность.
**Результат:** Отчет о поведении системы под нагрузкой и рекомендации по оптимизации.

```bash
#!/bin/bash  
  
# Задание 3: Анализ процессов и диагностика системы  
  
  
# ** Проверка наличия необходимых утилит **  
check_utilities() {  
  log_message "Начинаем проверку необходимых утилит (top, htop)"  
  UTILITIES=("top" "htop")  
  for util in "${UTILITIES[@]}"; do  
    command -v "$util" >/dev/null 2>&1 || { echo "Ошибка: утилита $util не найдена. Установите пакет необходимый пакет. Работа скрипта прекращена" >&2; exit 1; }  
  done  
  echo "Проверка наличия необходимых утилит завершена успешно."  
}  
  
  
# 1. Анализ ресурсов  
  
echo "##################################################"  
echo "# 1. Анализ ресурсов"  
echo "##################################################"  
  
# Функция для создания процессов, нагружающих CPU, память и диск  
create_load_processes() {  
  echo "Создание процессов, нагружающих систему..."  
  
  # Процесс, интенсивно использующий CPU (цикл while true)  
  echo "Запуск CPU-интенсивного процесса..."  
  while true; do :; done & cpu_pid=$!          # запускаем в фоне и сохраняем пид процесса в переменную  cpu_pid  
  echo "PID CPU-интенсивного процесса: $cpu_pid"  
  
  # Процесс, интенсивно использующий память (заполнение массива)  
  echo "Запуск Memory-интенсивного процесса..."  
  memory_array=()  
  for i in $(seq 1 100000); do memory_array+=("AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA"); done & memory_pid=$!  
  # аналогично запускаем в фоне и сохраняем пид процесса  
  echo "PID Memory-интенсивного процесса: $memory_pid"  
  
  # Процесс, интенсивно использующий диск (запись в файл)  
  echo "Запуск Disk-интенсивного процесса..."  
  dd if=/dev/urandom of=disk_load.tmp bs=1M count=100 & disk_pid=$!  
  # аналогично запускаем в фоне и сохраняем пид процесса  
  echo "PID Disk-интенсивного процесса: $disk_pid"  
  
  echo "Процессы для нагрузки системы запущены с PID: $cpu_pid, $memory_pid, $disk_pid"  
}  
  
# Запускаем процессы нагрузки  
create_load_processes  
  
echo ""  
echo "### Анализ с помощью top ###"  
echo "Запуск top в пакетном режиме для записи в файл top_output.log..."  
top -b -n 5 > top_output.log &  
echo "Для просмотра результатов анализа top, используйте: cat top_output.log"  
  
echo ""  
echo "### Анализ с помощью htop ###"  
echo "Запуск htop в пакетном режиме для записи в файл htop_output.log..."  
script -q -c "htop -n 5" htop_output.log &  
echo "Для просмотра результатов анализа htop, используйте: cat htop_output.log"  
  
echo ""  
echo"### Анализ через /proc ###"  
echo "Сбор информации из /proc для CPU-интенсивного процесса..."  
cat /proc/$cpu_pid/status > cpu_proc_status.log  
echo "Информация о статусе CPU-интенсивного процесса сохранена в cpu_proc_status.log"  
cat /proc/$cpu_pid/mem > cpu_proc_mem.log  
echo "Информация о памяти CPU-интенсивного процесса сохранена в cpu_proc_mem.log"  
  
echo ""  
echo ""  
echo ""  
echo "##################################################"  
print "# 2. Статусы процессов"  
print "##################################################"  
echo ""  
echo "### Создание процессов-зомби и сирот ###"  
  
# Создание процесса-зомби  
create_zombie_process() {  
  (  
    echo "Создание процесса-зомби..."  
    sleep 1  
    exit 0 # Процесс завершается, но родитель не собирает статус, становится зомби  
  ) & zombie_parent_pid=$!  
  
  sleep 2 # Даем время зомби-процессу стать зомби  
  
  echo "PID родительского процесса для зомби: $zombie_parent_pid"  
}  
  
# Создание сиротского процесса  
create_orphan_process() {  
  (  
    echo "Создание сиротского процесса..."  
    sleep 2  
    echo "Сиротский процесс работает..."  
    sleep 5  
    echo "Сиротский процесс завершен."  
  ) & orphan_pid=$!  
  
  echo "PID сиротского процесса: $orphan_pid"  
  
  sleep 1 # Даем время сиротскому процессу стать сиротой  
  echo "Завершение родительского процесса сироты..."  
  kill -9 $orphan_pid # Завершаем родителя, делая процесс сиротой  
}  
  
  
create_zombie_process  
create_orphan_process  
  
printf "\n### Анализ статусов процессов с помощью ps ###"  
echo "Вывод информации о зомби-процессах:"  
ps aux | grep 'Z'  
echo "Вывод информации о сиротских процессах (PPID=1):"  
ps aux | awk -v shell_pid=$$ '{if ($3!=0 && $4!=0 && $2 != shell_pid && $3==1) print $0}' # Ищем процессы с PPID=1, исключая idle и текущий скрипт  
  
echo ""  
echo ""  
echo ""  
echo "##################################################"  
echo "# 3. Использование strace"  
echo "##################################################"  
  
echo "### Анализ системных вызовов команды ls с помощью strace ###"  
echo "Запуск strace для команды ls и запись в log-файл strace_ls.log"  
strace ls > strace_ls.log 2>&1  
echo "Результаты strace сохранены в strace_ls.log"  
echo "Для просмотра результатов strace, используйте: less strace_ls.log"  
  
  
echo ""  
echo ""  
echo ""  
echo "##################################################"  
echo "# 4. Оптимизация"  
echo "##################################################"  
  
echo "### Изменение приоритета процесса с помощью nice ###"  
  
# Находим PID процесса, который мы хотим приоритизировать (например, CPU-интенсивный)  
# nice - указывает приоритет выполнения процессором различных задач, диапазон приоритетов -20 до 19  
echo "PID CPU-интенсивного процесса для nice: $cpu_pid"  
  
echo "Запуск CPU-интенсивного процесса с nice +10..."  
nice -n 10 while true; do :; done & nice_cpu_pid=$!  
echo "PID CPU-интенсивного процесса с nice: $nice_cpu_pid"  
  
echo "Сравните производительность процессов (обычного и с nice) в top или htop."  
echo "Обратите внимание на значения CPU% и PR (приоритет) в top/htop."  
  
echo "### Изменение приоритета процесса с помощью ionice ###"  
  
# Находим PID процесса, который интенсивно использует диск (disk_pid)  
# ionice - позволяет указать приоритет при операциях ввода/вывода, например чтобы снизить нагрузку на диск. Первым указывается класс от 1 до 3, потом приоритет от 0 до 7, где 7 наименьший.  
echo "PID Disk-интенсивного процесса для ionice: $disk_pid"  
  
echo "Запуск Disk-интенсивного процесса с ionice -c2 -n4 (best-effort)..."  
ionice -c2 -n4 dd if=/dev/urandom of=disk_load_ionice.tmp bs=1M count=100 & ionice_disk_pid=$!  
echo "PID Disk-интенсивного процесса с ionice: $ionice_disk_pid"  
  
echo "Сравните производительность процессов (обычного и с ionice) в iotop (если установлен) или top/htop, обратите внимание на значения IO."  
  
  
echo ""  
echo ""  
echo ""  
echo "##################################################"  
echo "# Завершение скрипта"  
echo "##################################################"  
  
echo "Для анализа производительности системы под нагрузкой:"  
echo "1. Просмотрите файлы top_output.log и htop_output.log для анализа ресурсов CPU, памяти, и IO."  
echo "2. Проанализируйте cpu_proc_status.log и cpu_proc_mem.log для деталей о CPU-интенсивном процессе."  
echo "3. Используйте ps aux | grep 'Z' и ps aux | awk '{if (\$3!=0 && \$4!=0 && \$2 != $$ && \$3==1) print $0}' для анализа зомби и сиротских процессов."  
echo "4. Изучите strace_ls.log для системных вызовов команды ls."  
echo "5. Сравните производительность обычного CPU-процесса и процесса с nice, а также обычного Disk-процесса и процесса с ionice, используя top/htop."  
  
echo "Скрипт завершен. Отчет о поведении системы под нагрузкой и рекомендации по оптимизации можно составить на основе собранных данных."
```



## <span style="color: #4CAF50;">_Задание 4 Автоматизация управления пользователями</span>
**Цель:** создать скрипт для управления пользователями системы.
1. Создание пользователей:
●       Напишите скрипт, который создает пользователей на основе данных из CSV файла.
●       Задайте каждому пользователю домашнюю директорию, сгенерируйте SSH-ключи и назначьте пароль.
2. Управление группами
●       Добавьте пользователей в группы в зависимости от их роли.
●       Проверьте и обновите права доступа на директории, связанные с группами.
3. Логи и отчеты
●       Все изменения записывайте в лог-файл (например, созданные пользователи, ошибки).
4. Удаление пользователей
●       Добавьте функционал для удаления пользователей, указанных в другом CSV файле, с возможностью удаления их домашней директории.

**Результат:** Готовый скрипт для массового управления пользователями.


```bash
#!/bin/bash  
  
# ** Настройки скрипта **  
LOG_FILE="user_management.log"  
USERS_CSV="users.csv"         # CSV файл для создания пользователей  
#username,firstname,lastname,role,groups  
#user1,Иван,Иванов,developer,developers,git_users  
#user2,Петр,Петров,tester,testers,qa_team  
  
DELETE_USERS_CSV="delete_users.csv" # CSV файл для удаления пользователей  
#    username,delete_home_dir  
#    user1,yes  
#    user2,no  
  
  
  
DEFAULT_HOME_DIR_PREFIX="/home"  
DEFAULT_GROUP="standard_users" # Группа по умолчанию для новых пользователей  
  
# ** Проверка наличия необходимых утилит **  
check_utilities() {  
  log_message "Начинаем проверку необходимых утилит..."  
  UTILITIES=("useradd" "usermod" "userdel" "openssl" "ssh-keygen")  
  for util in "${UTILITIES[@]}"; do  
    command -v "$util" >/dev/null 2>&1 || { echo "Ошибка: утилита $util не найдена. Установите пакет 'adduser', 'shadow' или 'openssh-client'." >&2; exit 1; }  
  done  
  log_message "Проверка утилит завершена успешно."  
}  
  
# ** Функция для логирования сообщений **  
log_message() {  
  TIMESTAMP=$(date +'%Y-%m-%d %H:%M:%S')  
  echo "$TIMESTAMP - $@" >> "$LOG_FILE"  
}  
  
# ** Функция создания домашней директории пользователя **  
# Предварительная настройка домашней директории  
create_home_directory() {  
  local username=$1  
  local home_dir="$DEFAULT_HOME_DIR_PREFIX/$username"  
  log_message "Создание домашней директории: $home_dir для пользователя $username"  
  if ! mkdir -p "$home_dir"; then  
    log_message "Ошибка при создании домашней директории для пользователя $username: $home_dir"  
    return 1  
  fi  
  log_message "Домашняя директория создана: $home_dir"  
  return 0  
}  
  
# ** Функция создания пользователя в системе **  
# Создание учетной записи пользователя и связывание с домашней директорией  
add_system_user() {  
  local username=$1  
  local home_dir="$DEFAULT_HOME_DIR_PREFIX/$username"  
  log_message "Добавление системного пользователя: $username с домашней директорией: $home_dir"  
  if useradd -m -d "$home_dir" -s /bin/bash "$username"; then  
    log_message "Пользователь $username успешно создан."  else  
    log_message "Ошибка при создании пользователя $username."  
    return 1  
  fi  
  
  log_message "Изменение владельца директории $home_dir на пользователя $username"  
  if chown "$username":"$username" "$home_dir"; then  
    log_message "Владелец директории $home_dir успешно изменен на пользователя $username"  
  else  
    log_message "Ошибка при изменении владельца директории $home_dir на пользователя $username"  
    return 1  
  fi  
  
  return 0  
}  
  
# ** Функция генерации SSH ключей для пользователя **  
generate_ssh_keys() {  
  local username=$1  
  local home_dir="$DEFAULT_HOME_DIR_PREFIX/$username"  
  local ssh_key_dir="$home_dir/.ssh"  
  log_message "Генерация SSH ключей для пользователя $username в директории: $ssh_key_dir"  
  mkdir -p "$ssh_key_dir"  
  chown "$username":"$username" "$ssh_key_dir"  
  chmod 700 "$ssh_key_dir"  
  
  if ssh-keygen -t rsa -b 2048 -N "" -f "$ssh_key_dir/id_rsa"; then  
    chown "$username":"$username" "$ssh_key_dir/id_rsa" "$ssh_key_dir/id_rsa.pub"  
    chmod 600 "$ssh_key_dir/id_rsa"  
    chmod 644 "$ssh_key_dir/id_rsa.pub"  
    log_message "SSH ключи сгенерированы для пользователя $username."  
    return 0  
  else  
    log_message "Ошибка при генерации SSH ключей для пользователя $username."  
    return 1  
  fi  
}  
  
# ** Функция установки пароля для пользователя **  
set_user_password() {  
  local username=$1  
  log_message "Установка пароля для пользователя $username"  
  password=$(openssl rand -base64 12)  
  echo "Сгенерированный пароль для пользователя $username: $password" >> "$LOG_FILE" # Добавлена запись в лог!  
  if echo "$username:$password" | chpasswd; then  
    log_message "Пароль установлен для пользователя $username."  
    return 0  
  else  
    log_message "Ошибка при установке пароля для пользователя $username."  
    return 1  
  fi  
}  
  
# ** Функция добавления пользователя в группы (с проверкой и созданием групп) **  
add_user_to_groups() {  
  local username=$1  
  local groups=$2  
  default_groups="$DEFAULT_GROUP,$groups"  
  group_list=$(echo "$default_groups" | tr ',' ' ')  
  
  log_message "Начинаем проверку и создание групп при необходимости для пользователя $username: $group_list"  
  
  # Итерируем по списку групп  
  for group_name in $group_list; do  
    # Проверяем существование группы  
    if ! getent group "$group_name" > /dev/null 2>&1; then  
      log_message "Группа '$group_name' не существует. Создаем группу."  
      # Создаем группу, если она не существует  
      if ! groupadd "$group_name"; then  
        log_message "Ошибка при создании группы '$group_name'!"  
        return 1 # В случае ошибки создания группы, выходим из функции с ошибкой  
      else  
        log_message "Группа '$group_name' успешно создана."  
      fi  
    else      log_message "Группа '$group_name' существует."  
    fi  
  done  
  log_message "Все необходимые группы существуют. Добавляем пользователя $username в группы: $group_list"  
  if usermod -a -G "$default_groups" "$username"; then  
    log_message "Пользователь $username успешно добавлен в группы: $group_list"  
    return 0  
  else  
    log_message "Ошибка при добавлении пользователя $username в группы: $group_list"  
    return 1  
  fi  
}  
  
# ** Функция обновления прав доступа для групп **  
update_group_directory_permissions() {  
  local groups=$1  
  for group in $(echo "$groups" | tr ',' ' '); do  
    group_dir="/opt/$group" # Пример пути, измените при необходимости  
    if ! mkdir -p "$group_dir"; then  
       log_message "Ошибка при создании директории для группы $group"  
    else  
      log_message "Cоздана директория для группы $group: $group_dir"  
    fi  
  
    if [ -d "$group_dir" ]; then  
      chown -R :"$group" "$group_dir"  
      chmod -R g+rwx "$group_dir"  
      log_message "Права доступа для директории $group_dir для группы $group обновлены ."    else  
      log_message "Предупреждение: директория $group_dir для группы $group не найдена."    fi  
  done  return 0  
}  
  
  
# ** Основная функция создания пользователя, оркестрирует процесс **  
create_user() {  
  local username=$1  
  local firstname=$2  
  local lastname=$3  
  local role=$4  
  local groups=$5  
  
  log_message "Начинаем процесс создания пользователя: $username"  
  
  create_home_directory "$username"        || return 1 # Создаем домашнюю директорию или выходим  
  add_system_user "$username"              || return 1 # Добавляем пользователя в систему или выходим  
  generate_ssh_keys "$username"            || return 1 # Генерируем SSH ключи или выходим  
  set_user_password "$username"            || return 1 # Устанавливаем пароль или выходим  
  add_user_to_groups "$username" "$groups" || return 1 # Добавляем в группы или выходим  
  
  update_group_directory_permissions "$groups" # Обновляем права доступа (не критично, поэтому || return 1 не нужен)  
  
  log_message "Создание пользователя $username завершено успешно."  return 0  
}  
  
  
# ** Функция обработки CSV файла создания пользователей **  
process_create_users_csv() {  
  if [ ! -f "$USERS_CSV" ]; then  
    log_message "Ошибка: CSV файл для создания пользователей не найден: $USERS_CSV"  
    echo "Ошибка: CSV файл $USERS_CSV не найден. Пожалуйста, проверьте наличие файла." >&2  
    return 1  
  fi  
  
  log_message "Начинаем обработку CSV файла для создания пользователей: $USERS_CSV"  
  
  while IFS=',' read -r username firstname lastname role groups; do  
    if [[ -n "$username" ]]; then # Пропускаем пустые строки  
      log_message "*************** Обработка строки CSV для пользователя: $username ***************"      if ! create_user "$username" "$firstname" "$lastname" "$role" "$groups"; then  
        log_message "Ошибка при создании пользователя $username из CSV. Проверьте детали в лог-файле."      fi  
    fi  done < "$USERS_CSV"  
  
  log_message "Завершена обработка CSV файла для создания пользователей."  
  return 0  
}  
  
  
  
  
# ** Функция удаления домашней директории пользователя **  
remove_home_directory() {  
  echo "удаление домашних директорий"  
  local username=$1  
  local home_dir="$DEFAULT_HOME_DIR_PREFIX/$username"  
  log_message "Удаление домашней директории: $home_dir для пользователя $username"  
  if [ -d "$home_dir" ]; then  
    if rm -rf "$home_dir"; then  
      log_message "Домашняя директория пользователя $username удалена: $home_dir"  
      return 0  
    else  
      log_message "Ошибка при удалении домашней директории пользователя $username: $home_dir"  
      return 1  
    fi  
  else    log_message "Предупреждение: Домашняя директория пользователя $username не найдена: $home_dir"  
    return 0 # Не ошибка, если директория уже не существует  
  fi  
}  
  
  
# ** Функция удаления пользователя из системы (с удалением групп пользователя) **  
delete_system_user() {  
  local username=$1  
  log_message "Удаление системного пользователя: $username"  
  
  # Получаем список групп пользователя  
  local user_groups=$(id -Gn "$username")  
  echo $user_groups  
  
  # Проверяем и удаляем группы пользователя (если они пустые и не primary)  
  if [[ -n "$user_groups" ]]; then # Если пользователь состоит в каких-либо группах  
    log_message "Проверка и удаление групп пользователя: $username"  
    for group_name in $user_groups; do  
      log_message "Попытка удаления группы: $group_name"  
      if groupdel "$group_name"; then  
        log_message "Группа '$group_name' успешно удалена."  
      else  
        log_message "Не удалось удалить группу '$group_name'. Возможно, в группе еще есть пользователи или это primary group."  
      fi  
    done  fi  
  # Удаляем пользователя  
  if userdel "$username"; then  
    log_message "Пользователь $username успешно удален."    return 0  
  else  
    log_message "Ошибка при удалении пользователя $username."  
    return 1  
  fi  
}  
  
  
# ** Основная функция удаления пользователя, оркестрирует процесс **  
delete_user() {  
  local username=$1  
  local delete_home_dir=$2 # 'yes' для удаления домашней директории, иначе 'no'  
  
  log_message "Начинаем процесс удаления пользователя: $username"  
  
  if ! delete_system_user "$username"; then  
    return 1  
  fi  
  
  if [ "$delete_home_dir" == "yes" ]; then  
    if ! remove_home_directory "$username"; then  
      return 1  
    fi  
  fi  
  log_message "Удаление пользователя $username завершено."  return 0  
}  
  
  
# ** Функция обработки CSV файла удаления пользователей **  
process_delete_users_csv() {  
  if [ ! -f "$DELETE_USERS_CSV" ]; then  
    log_message "Ошибка: CSV файл для удаления пользователей не найден: $DELETE_USERS_CSV"  
    echo "Ошибка: CSV файл $DELETE_USERS_CSV не найден. Пожалуйста, проверьте наличие файла." >&2  
    return 1  
  fi  
  
  log_message "Начинаем обработку CSV файла для удаления пользователей: $DELETE_USERS_CSV"  
  
  while IFS=',' read -r username delete_home_dir; do  
    if [[ -n "$username" ]]; then # Пропускаем пустые строки  
      log_message "Обработка строки CSV для удаления пользователя: $username"  
      if ! delete_user "$username" "$delete_home_dir"; then  
        log_message "Ошибка при удалении пользователя $username из CSV. Проверьте детали в лог-файле."      fi  
    fi  done < "$DELETE_USERS_CSV"  
  
  log_message "Завершена обработка CSV файла для удаления пользователей."  
  return 0  
}  
  
  
  
# ** Главная функция скрипта **  
main() {  
  echo "Скрипт управления пользователями запущен."  
  log_message "Скрипт управления пользователями запущен."  
  
  check_utilities # Проверка наличия необходимых утилит в начале работы  
  
  case "$1" in  
    adduser)  
      log_message "Этап: Создание пользователей из CSV"  
      process_create_users_csv  
    ;;  
    deluser)  
      log_message "Этап: Удаление пользователей из CSV"  
      process_delete_users_csv  
    ;;  
    *)  
      echo "выберете действие добавление (adduser) или удаление (deluser) пользователей"  
    ;;  
  esac  
  
  
  
  echo "Скрипт управления пользователями завершен. Детали смотрите в лог-файле: $LOG_FILE"  
  log_message "Скрипт управления пользователями завершен."  
}  
  
# ** Запуск главной функции **  
main "$@"  
  
exit 0
```


```users.csv
user1,Иван,Иванов,developer,developers,git_users  
user2,Петр,Петров,tester,testers,qa_team  
user3,Анна,Сидорова,manager,managers,admin_users  
user4,Елена,Смирнова,developer,developers,backend_dev  
user5,Алексей,Кузнецов,tester,testers,performance_testers  
user6,Ольга,Васильева,manager,managers,project_managers  
user7,Дмитрий,Соколов,developer,developers,frontend_dev,git_users  
user8,Наталья,Попова,tester,testers,security_testers,qa_team  
user9,Сергей,Лебедев,manager,managers,hr_managers,admin_users  
user10,Мария,Морозова,developer,developers,database_dev,backend_dev
```


```delete_users.csv
user1,yes  
user2,yes  
user3,yes  
user4,yes  
user5,yes  
user6,yes  
user7,yes  
user8,yes  
user9,yes  
user10,yes
```







