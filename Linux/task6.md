# <span style="color: #ff5733; #ff5733">_РАЗДЕЛ 6 Процессы</span>

## <span style="color: 4CAF50;">_Задание 1 Введение в процессы</span>
### <span style="color: 2196F3;">_1. Определите, какие процессы выполняются в вашей системе:</span>
●       Выполните команду ps -aux и найдите ваш shell-процесс.
` user@mypc  ~  ps -aux | grep "user "`
`user 1672  0.0  0.0  13928  7344 pts/0    Ss   07:25   0:01 -zsh`

●       Найдите процессы с самым высоким использованием CPU и памяти.

**1. Процессы с высоким использованием CPU**
	`ps aux --sort=-%cpu | head -n 10`
	- **Описание команды**:
	    - `ps aux`: Выводит список всех процессов с детальной информацией.
	    - `--sort=-%cpu`: Сортирует процессы по убыванию использования CPU.
	    - `head -n 10`: Показывает первые 10 строк (топ-10 процессов).
	```
	 user@mypc  ~  ps -aux --sort=-%cpu | head -n 10                                                                                 
	
	USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND                                                         
	
	systemd+     706  0.1  0.0  14836  6784 ?        Ss   07:24   0:03 /lib/systemd/systemd-oomd                                       
	
	root         773  0.1  0.4 1469288 34124 ?       Ssl  07:24   0:03 /usr/lib/snapd/snapd                                            
	
	root           1  0.0  0.1 168300 13316 ?        Ss   07:24   0:02 /sbin/init splash                                               
	
	root           2  0.0  0.0      0     0 ?        S    07:24   0:00 [kthreadd]                                                      
	
	root           3  0.0  0.0      0     0 ?        S    07:24   0:00 [pool_workqueue_release]                                        
	
	root           4  0.0  0.0      0     0 ?        I<   07:24   0:00 [kworker/R-rcu_g]                                               
	
	root           5  0.0  0.0      0     0 ?        I<   07:24   0:00 [kworker/R-rcu_p]                                               
	
	root           6  0.0  0.0      0     0 ?        I<   07:24   0:00 [kworker/R-slub_]                                               
	
	root           7  0.0  0.0      0     0 ?        I<   07:24   0:00 [kworker/R-netns]
	```
	

**2. Процессы с высоким использованием памяти**
	`ps aux --sort=-%mem | head -n 10`
	- **Описание команды**:
	    - `--sort=-%mem`: Сортирует процессы по убыванию использования памяти.
	    - Остальные параметры аналогичны предыдущей команде.
	```
	 user@mypc  ~  ps aux --sort=-%mem | head -n 10                                                                                  
	
	USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND                                                         
	
	gdm         1183  0.0  2.5 4309000 207212 tty1   Sl+  07:24   0:02 /usr/bin/gnome-shell                                            
	
	gdm         1056  0.0  0.8 302816 72824 tty1     Sl+  07:24   0:00 /usr/lib/xorg/Xorg vt1 -displayfd 3 -auth /run/user/128/gdm/Xaut
	
	hority -nolisten tcp -background none -noreset -keeptty -novtswitch -verbose 3                                                     
	
	root         773  0.1  0.4 1469288 34124 ?       Ssl  07:24   0:03 /usr/lib/snapd/snapd                                            
	
	gdm         1253  0.0  0.3 2734584 27816 tty1    Sl+  07:24   0:00 /usr/bin/gjs /usr/share/gnome-shell/org.gnome.Shell.Notification
	
	s                                                                                                                                  
	
	gdm         1408  0.0  0.3 2669180 27616 tty1    Sl+  07:24   0:00 /usr/bin/gjs /usr/share/gnome-shell/org.gnome.ScreenSaver       
	
	gdm         1042  0.0  0.2 823068 24076 ?        S
	
	root        1037  0.0  0.2  86752 23936 ?        Ss   07:24   0:00 /usr/sbin/smbd --foreground --no-process-group                  
	
	root         873  0.0  0.2 118196 23552 ?        Ssl  07:24   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgra
	
	de-shutdown --wait-for-signal                                                                                                      
	
	user        1523  0.0  0.2 297036 22804 ?        Ssl  07:25   0:00 /usr/bin/pulseaudio --daemonize=no --log-target=journal
	```
### <span style="color: 2196F3;">_2. Выведите дерево процессов:</span>
●       Используйте команду pstree и определите родительские процессы для запущенных приложений.

**Как найти родительский процесс для конкретного приложения**
Запустите `pstree` с PID и аргументами:
`pstree -aps $(pgrep htop)`
- `pgrep htop` возвращает PID процесса htop.
- Опции `-a` (аргументы) и `-s` (показать предков процесса) раскроют цепочку родительских процессов.
```
 user@mypc  ~  pstree -aps $(pgrep htop)                                  
systemd,1 splash                                                            
  └─sshd,888                                                                
      └─sshd,1510                                                           
          └─sshd,1666                                                       
              └─zsh,1672                                                    
                  └─htop,3035
```
## <span style="color: 4CAF50;">_Задание 2 Создание и управление процессами</span>

1.     Запустите команду в фоновом режиме:

●       Выполните ping google.com > output.log &.
``` bash
user@mypc:~$ ping google.com > output.log &
[1] 5518
user@mypc:~$
```
`&` — символ, который отправляет процесс в фоновый режим.
`[1]` — это номер задания (job number), а `5518` — PID (идентификатор процесса).

●       Переведите процесс на передний план.
Чтобы перевести процесс на передний план, используйте команду `fg`:
user@mypc:~$ fg
ping google.com > output.log

Если у вас несколько фоновых задач, вы можете указать номер задачи, например:
`fg %1`

●       Приостановите выполнение и возобновите в фоновом режиме.
Чтобы приостановить выполнение процесса, нажмите комбинацию клавиш:
`Ctrl + Z`
```
^Z
[1]+  Stopped                 ping google.com > output.log
user@mypc:~$
```

Чтобы возобновить выполнение процесса в фоновом режиме, используйте команду `bg`:
```
user@mypc:~$ bg
[1]+ ping google.com > output.log &
user@mypc:~$
```

Если у вас несколько остановленных задач, вы можете указать номер задачи, например:
`bg %1`

Чтобы проверить текущие фоновые и остановленные задачи, используйте команду: `jobs`
```
user@mypc:~$ jobs
[1]+  Running                 ping google.com > output.log &
user@mypc:~$
```
Она покажет список всех задач с их состоянием (например, `Running`, `Stopped`).



2.     Установите приоритеты процессам:

●       Запустите команду yes > /dev/null &.
Команда `yes` генерирует бесконечный поток символов "y". Чтобы запустить её в фоновом режиме и перенаправить вывод в `/dev/null` (чтобы он не засорял терминал), выполните:
```bash
user@mypc:~$ yes > /dev/null &
[1] 8138
user@mypc:~$

```

●       Используйте top, чтобы найти процесс, и измените его приоритет с помощью renice.
Запустите утилиту `top`, чтобы просмотреть список активных процессов:
```bash
user@mypc:~$ top
top - 08:52:16 up 26 min,  1 user,  load average: 0,98, 0,44, 0,17
Tasks: 243 total,   2 running, 241 sleeping,   0 stopped,   0 zombie
%Cpu(s):  9,4 us, 15,9 sy,  0,0 ni, 74,6 id,  0,0 wa,  0,0 hi,  0,0 si,  0,1 st
MiB Mem :   7941,7 total,   6673,2 free,    523,7 used,    744,9 buff/cache
MiB Swap:   2048,0 total,   2048,0 free,      0,0 used.   7174,6 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
   8138 user      20   0    8372   1920   1920 R  99,7   0,0   2:43.85 yes
   9095 user      20   0   13252   4224   3328 R   0,3   0,1   0:00.01 top
      1 root      20   0  166876  12032   8320 S   0,0   0,1   0:01.51 systemd
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.00 kthreadd
      3 root      20   0       0      0      0 S   0,0   0,0   0:00.00 pool_workqueue_release
      4 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R-rcu_g
      5 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R-rcu_p
      6 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R-slub_
      7 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R-netns
...
```

Вывод будет содержать таблицу с информацией о процессах. Найдите процесс `yes` по имени или PID. Обратите внимание на столбец `NI` (Nice value) — это текущий приоритет процесса.
По умолчанию значение `NI` для большинства процессов равно `0`. Чем выше значение `NI`, тем ниже приоритет процесса.
Нажмите `q`, чтобы выйти из `top`.

Чтобы изменить приоритет процесса, используйте команду `renice`. Например, увеличим приоритет (сделаем его ниже) до значения `10`:

```
user@mypc:~$ renice 10 -p 8138
8138 (process ID) old priority 0, new priority 10
user@mypc:~$
```
Здесь:
- `10` — новое значение приоритета (Nice value).
- `-p 8138` — указание PID процесса.

Вы увидите подтверждение:
`8138 (process ID) old priority 0, new priority 10`

Теперь процесс `yes` работает с более низким приоритетом.
```

user@mypc:~$ top
top - 08:55:36 up 29 min,  1 user,  load average: 1,08, 0,74, 0,35
Tasks: 243 total,   2 running, 241 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0,1 us, 16,1 sy,  9,1 ni, 74,7 id,  0,0 wa,  0,0 hi,  0,0 si,  0,0 st
MiB Mem :   7941,7 total,   6670,2 free,    526,6 used,    744,9 buff/cache
MiB Swap:   2048,0 total,   2048,0 free,      0,0 used.   7171,6 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
   8138 user      30  10    8372   1920   1920 R 100,0   0,0   6:03.70 yes
  10223 user      20   0   13248   4224   3328 R   0,7   0,1   0:00.03 top
   1862 user      20   0   17684   8360   5760 S   0,3   0,1   0:01.01 sshd
      1 root      20   0  166876  12032   8320 S   0,0   0,1   0:01.51 systemd
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.00 kthreadd
      3 root      20   0       0      0      0 S   0,0   0,0   0:00.00 pool_workqueue_release
      4 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R-rcu_g
      5 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R-rcu_p
...
```


3.     Остановите процесс:

●       Найдите PID с помощью ps и завершите процесс с помощью kill.
```
user@mypc:~$ ps
    PID TTY          TIME CMD
   1866 pts/0    00:00:01 zsh
   5385 pts/0    00:00:00 bash
   8138 pts/0    00:07:05 yes
  10686 pts/0    00:00:00 ps
user@mypc:~$
```
или
```
user@mypc:~$ ps aux | grep yes
user        8138  100  0.0   8372  1920 pts/0    RN   08:49   7:52 yes
user       10965  0.0  0.0   9216  2560 pts/0    S+   08:57   0:00 grep --color=auto yes
```


Используйте команду `kill`, чтобы завершить процесс по его PID:
```
user@mypc:~$ kill 8138
user@mypc:~$
[1]+  Terminated              yes > /dev/null
user@mypc:~$ jobs
user@mypc:~$

```



## <span style="color: 4CAF50;">_Задание 3 Мониторинг процессов</span>

1.     Запустите top и ответьте на вопросы:
```bash
user@mypc:~$ top
top - 09:35:38 up  1:09,  1 user,  load average: 0,01, 0,02, 0,10
Tasks: 242 total,   1 running, 241 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0,1 us,  0,2 sy,  0,0 ni, 99,7 id,  0,0 wa,  0,0 hi,  0,0 si,  0,0 st

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
   1883 user      20   0   10620   3840   3584 S   0,3   0,0   0:06.09 zsh
  24546 user      20   0   13248   4224   3328 R   0,3   0,1   0:00.12 top
      1 root      20   0  166876  12032   8320 S   0,0   0,1   0:01.54 systemd
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.00 kthreadd
      3 root      20   0       0      0      0 S   0,0   0,0   0:00.00 pool_workqueue_release
      4 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R-rcu_g
      5 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R-rcu_p
      6 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R-slub_
      7 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R-netns

```
●       Какие процессы занимают больше всего CPU?
1883 - zsh
24546 - top
1 - systemd

Чтобы отсортировать процессы по загрузке процессора (CPU):
1. горячая клавиша - P (заглавная буква)
2. использование флага -o при запуске - `top -o %CPU`

Дополнительные полезные клавиши в `top`:
- **`M`** : Сортировка по использованию памяти (RAM).
- **`N`** : Сортировка по PID (идентификатору процесса).
- **`T`** : Сортировка по времени работы (TIME+).

●       Как меняется нагрузка, если запустить stress или yes > /dev/null &?
```bash
user@mypc:~$ stress --cpu 2
stress: info: [28363] dispatching hogs: 2 cpu, 0 io, 0 vm, 0 hdd
```

```bash
user@mypc:~$ top
top - 09:45:35 up  1:19,  2 users,  load average: 1,08, 0,31, 0,16
Tasks: 252 total,   3 running, 249 sleeping,   0 stopped,   0 zombie
%Cpu(s): 50,0 us,  0,2 sy,  0,0 ni, 49,6 id,  0,0 wa,  0,0 hi,  0,1 si,  0,0 st
MiB Mem :   7941,7 total,   6556,5 free,    538,0 used,    847,2 buff/cache
MiB Swap:   2048,0 total,   2048,0 free,      0,0 used.   7155,8 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  28364 user      20   0    3708    256    256 R 100,0   0,0   0:40.26 stress
  28365 user      20   0    3708    256    256 R 100,0   0,0   0:40.27 stress
   1862 user      20   0   17684   8488   5760 S   0,3   0,1   0:02.95 sshd
      1 root      20   0  166876  12032   8320 S   0,0   0,1   0:01.58 systemd
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.00 kthreadd
      3 root      20   0       0      0      0 S   0,0   0,0   0:00.00 pool_workqueue_release
      4 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R-rcu_g

```
увеличивается LA


2.     Используйте htop для мониторинга:

●       Найдите процесс, идущий в фоновом режиме.
Чтобы определить фоновые процессы в `htop`:
1. Проверьте столбец **`STAT`** на наличие значений `S` или `R`.
2. Проверьте столбец **`TTY`** на наличие `-` или `?` (значит процесс  не связан с терминалом)
3. Используйте поиск (**`F3`** ) для поиска конкретных процессов.
4. Проверьте столбец **`PPID`**, чтобы найти процессы, запущенные из вашей оболочки.


●       Измените приоритет (Nice) прямо в интерфейсе.
Чтобы изменить приоритет процесса, выполните следующие действия:
1. Выберите процесс, перемещаясь с помощью стрелок или кликнув на него мышью.
2. Нажмите клавишу **`F7`** для увеличения приоритета (делает процесс более важным) или **`F8`** для уменьшения приоритета (делает процесс менее важным).
    - Приоритет Nice может принимать значения от `-20` (наивысший приоритет) до `19` (наименьший приоритет).
    - Обратите внимание, что для установки отрицательного значения Nice вам может потребоваться привилегия суперпользователя (`sudo`).
    Обычные пользователи могут **только понизить приоритет** (увеличить значение nice - **F8**), но не повысить его (уменьшить nice).

	При нажатии F8 приоритет процесса пользователя понижался, при нажатии F7 приоритет пользователя не повышается, нужны привилегии рута.
29. После изменения приоритета, вы увидите обновленное значение в столбце **`PRI`** (приоритет) и **`NI`** (Nice).
	значения в столбцах меняются: 
	- Nice value = -20 → PRI = 0
	- Nice value = 0 → PRI = 20
	- Nice value = 19 → PRI = 39




3.     Изучите историю работы системы с помощью atop:
Утилита **`atop`** — это мощный инструмент для мониторинга производительности системы в реальном времени и сбора исторических данных. Она предоставляет подробную информацию о работе процессора, памяти, дисков, сетевых интерфейсов и других ресурсов системы. В отличие от `top` или `htop`, `atop` может записывать данные в лог-файлы, что позволяет анализировать нагрузку на систему за прошедшие периоды.
- Логи создаются ежедневно.
- Интервал записи — 10 минут (т.е. каждые 10 минут система фиксирует текущее состояние). По умолчанию `atop` записывает данные каждые 10 минут. Если вам нужно более частое или менее частое логирование, измените интервал записи. Файл конфигурации: `/etc/default/atop` `sudo nano /etc/default/atop` Найдите строку `INTERVAL` и измените значение:
	- `INTERVAL=5` — запись каждые 5 минут.
	- `INTERVAL=60` — запись каждые 60 минут.

	Для записи с интервалом в секунды, надо запускать из командной строки с помощью опции `-r`:
	`sudo atop -w /var/log/atop.log -r 1`
	- `-w /var/log/atop.log`: Указывает файл для записи логов.
	- `-r 1`: Устанавливает интервал записи в 1 секунду.
	Этот метод подходит, если вы хотите временно изменить интервал записи без изменения системных настроек.

- Максимальный размер директории с логами ограничен (обычно 1 ГБ).



●       Установите atop и запустите его с записью логов.

●       Откройте лог и проанализируйте данные.

**Просмотр лога за конкретную дату:**
`atop -r /var/log/atop/atop_YYYYMMDD`

**Основные команды для анализа логов**
После открытия лога через `atop`, можно использовать следующие горячие клавиши:
- **t** или **T** : Переключение между временными метками вперед/назад.
- **p** : Показать только процессы.
- **d** : Показать только дисковую активность.
- **n** : Показать только сетевую активность.
- **m** : Показать только использование памяти.
- **c** : Показать только использование CPU.
- **s** : Сортировка процессов по различным параметрам (CPU, память, диск и т.д.).
- **q** : Выход из программы.


**Поиск аномалий**
`atop` автоматически выделяет аномальные события, такие как высокая загрузка CPU, переполнение памяти или долгие операции ввода-вывода. Для поиска таких событий:
1. Откройте лог через `atop -r`.
2. Используйте клавишу **a** для переключения между "аномальными" точками времени.
3. В эти моменты `atop` автоматически подсветит процессы, которые больше всего влияют на систему. 



**Типичные сценарии анализа**
 a) Диагностика высокой загрузки CPU:
1. Откройте лог за период повышенной нагрузки.
2. Найдите моменты, когда CPU% близок к 100%.
3. Используйте клавишу **c** для сортировки процессов по использованию CPU.
b) Анализ использования памяти:
4. Откройте лог.
5. Проверьте значения SWAP и FREE (свободная память).
6. Если SWAP активно используется, найдите процессы с большим потреблением памяти.
c) Поиск медленных дисковых операций:
7. Откройте лог.
8. Проверьте столбцы DSK (например, await — время ожидания операций).
9. Найдите процессы с высоким числом чтений/записей.
d) Мониторинг сетевой активности:
10. Откройте лог.
11. Проверьте столбцы NET (например, RX/TX — входящий/исходящий трафик).
12. Найдите процессы с высокой сетевой нагрузкой.


## <span style="color: 4CAF50;">_Задание 4 Файлы и процессы</span>

### <span style="color: 2196F3;">_1. Исследуйте директорию /proc:</span>

●       Найдите ваш shell-процесс с помощью команды ps.
```
user@mypc:~$ ps
    PID TTY          TIME CMD
   1725 pts/0    00:00:00 zsh
   2553 pts/0    00:00:00 bash
   2632 pts/0    00:00:00 ps

```
PID=2553

●       Откройте /proc/[PID]/stat и объясните значения.
```
user@mypc:~$ cat /proc/2553/stat
2553 (bash) S 1725 2553 1725 34816 2553 4194304 754 836 2 1 1 0 0 1 20 0 1 0 25121 11550720 1344 18446744073709551615 103709335519232 103709336432493 140736311860928 0 0 0 0 3686404 1266761467 1 0 0 17 0 0 0 0 0 0 103709336677008 103709336725072 103709488152576 140736311867074 140736311867079 140736311867079 140736311869418 0

```


●       Определите, какие файлы открыты процессом, используя lsof.
```
user@mypc:~$ lsof -p 2553
lsof: WARNING: can't stat() fuse.portal file system /run/user/128/doc
      Output information may be incomplete.
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF    NODE NAME
bash    2553 user  cwd    DIR    8,3     4096  917506 /home/user
bash    2553 user  rtd    DIR    8,3     4096       2 /
bash    2553 user  txt    REG    8,3  1396520 1180302 /usr/bin/bash
bash    2553 user  mem    REG    8,3  5716816 1218539 /usr/lib/locale/locale-archive
bash    2553 user  mem    REG    8,3  2220400 1181790 /usr/lib/x86_64-linux-gnu/libc.so.6
bash    2553 user  mem    REG    8,3   200136 1207837 /usr/lib/x86_64-linux-gnu/libtinfo.so.6.3
bash    2553 user  mem    REG    8,3    27002 1182159 /usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache
bash    2553 user  mem    REG    8,3   240936 1181778 /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
bash    2553 user    0u   CHR  136,0      0t0       3 /dev/pts/0
bash    2553 user    1u   CHR  136,0      0t0       3 /dev/pts/0
bash    2553 user    2u   CHR  136,0      0t0       3 /dev/pts/0
bash    2553 user  255u   CHR  136,0      0t0       3 /dev/pts/0

```

**Разбор столбцов вывода:**
1. **COMMAND** :
    - Имя команды или процесса, который открыл файл.
    - Например: `bash`.
2. **PID** :
    - Идентификатор процесса.
    - Например: `2553`.
3. **USER** :
    - Пользователь, запустивший процесс.
    - Например: `user`.
4. **FD** (File Descriptor):
    - Дескриптор файла. Это ключевой столбец, который показывает, как используется файл:
        - `cwd`: Текущая рабочая директория процесса.
        - `rtd`: Корневая директория процесса.
        - `txt`: Файл текстового сегмента (например, исполняемый файл).
        - `mem`: Отображённый в память файл (например, библиотека).
        - Числа (`0`, `1`, `2`): Стандартные потоки ввода, вывода и ошибок.
            - `0u`: stdin (ввод).
            - `1u`: stdout (вывод).
            - `2u`: stderr (ошибки).
        - Буквы после чисел:
            - `r`: Чтение.
            - `w`: Запись.
            - `u`: Чтение и запись.
5. **TYPE** :
    - Тип файла:
        - `DIR`: Директория.
        - `REG`: Обычный файл.
        - `CHR`: Символьное устройство.
        - `FIFO`: Именованный канал.
        - `IPv4`/`IPv6`: Сетевое соединение.
6. **DEVICE** :
    - Устройство, на котором находится файл.
    - Например: `8,1` (первое разделение диска `/dev/sda1`).
7. **SIZE/OFF** :
    - Размер файла или смещение в файле.
    - Например: `4096` байт.
8. **NODE** :
    - Идентификатор inode файла.
    - Например: `12345`.
9. **NAME** :
    - Имя файла или ресурса.
    - Например: `/home/user/.bashrc`.


### <span style="color: 2196F3;">_2. Посмотрите открытые файловые дескрипторы:</span>

●       Запустите cat > /dev/null & в фоне.
```
user@mypc:~$ cat > /dev/null &
[1] 17607

```

●       Откройте /proc/[PID]/fd и проверьте, какие файлы открыты.
```
user@mypc:~$ ls /proc/17607/fd
0  1  2

user@mypc:~$ ls -l /proc/17607/fd
total 0
lrwx------ 1 user user 64 фев  6 04:24 0 -> /dev/pts/0
l-wx------ 1 user user 64 фев  6 04:24 1 -> /dev/null
lrwx------ 1 user user 64 фев  6 04:24 2 -> /dev/pts/0

```
Это список файловых дескрипторов, открытых процессом с PID `17607`. Каждый файловый дескриптор представляет собой ссылку на файл или ресурс, который процесс использует. В данном случае процесс открыл три стандартных дескриптора:
1. **`0`:**
    - Это стандартный ввод (`stdin`).
    - Обычно используется для чтения данных, например, из терминала или файла.
2. **`1`:**
    - Это стандартный вывод (`stdout`).
    - Обычно используется для вывода нормальных данных, например, в терминал.
3. **`2`:**
    - Это стандартный поток ошибок (`stderr`).
    - Обычно используется для вывода сообщений об ошибках.


 **Почему это важно?**
	1. **Отладка:**
	    - Если вы видите только стандартные дескрипторы (`0`, `1`, `2`), это может означать, что процесс не открывает дополнительных файлов или ресурсов. Это полезно для диагностики проблем, связанных с открытием файлов.
	2. **Мониторинг:**
	    - Вы можете использовать эту информацию для мониторинга того, какие файлы или ресурсы использует процесс.
	3. **Управление ресурсами:**
        - Если процесс открывает слишком много файлов (например, сотни или тысячи), это может указывать на утечку ресурсов (file descriptor leak).


### <span style="color: 2196F3;">_3. Проанализируйте использование памяти:</span>

●       Изучите содержимое /proc/meminfo и сравните с данными в free -h.
```
user@mypc:~$ cat /proc/meminfo
MemTotal:        8132312 kB
MemFree:         6883040 kB
MemAvailable:    7322924 kB
Buffers:           43520 kB
Cached:           614152 kB
SwapCached:            0 kB
Active:           779676 kB
Inactive:         198568 kB
Active(anon):     332020 kB
Inactive(anon):        0 kB
Active(file):     447656 kB
Inactive(file):   198568 kB
Unevictable:       13648 kB
Mlocked:           13648 kB
SwapTotal:       2097148 kB
SwapFree:        2097148 kB
Zswap:                 0 kB
Zswapped:              0 kB
Dirty:                 0 kB
Writeback:             0 kB
AnonPages:        334260 kB
Mapped:           256508 kB
Shmem:              7388 kB
KReclaimable:      37192 kB
Slab:             152248 kB
SReclaimable:      37192 kB
SUnreclaim:       115056 kB
KernelStack:        6708 kB
PageTables:        14932 kB
SecPageTables:         0 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:     6163304 kB
Committed_AS:    2343292 kB
VmallocTotal:   34359738367 kB
VmallocUsed:       27064 kB
VmallocChunk:          0 kB
Percpu:             2880 kB
HardwareCorrupted:     0 kB
AnonHugePages:         0 kB
ShmemHugePages:        0 kB
ShmemPmdMapped:        0 kB
FileHugePages:         0 kB
FilePmdMapped:         0 kB
Unaccepted:            0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
Hugetlb:               0 kB
DirectMap4k:      134988 kB
DirectMap2M:     3010560 kB
DirectMap1G:     7340032 kB


user@mypc:~$ free -h
               total        used        free      shared  buff/cache   available
Mem:           7,8Gi       541Mi       6,6Gi       7,0Mi       678Mi       7,0Gi
Swap:          2,0Gi          0B       2,0Gi

```
 **Содержимое `/proc/meminfo`**
Файл `/proc/meminfo` — это виртуальный файл, предоставляемый ядром Linux, который содержит подробную информацию о состоянии памяти системы. Этот файл используется многими утилитами (например, `free`, `top`, `htop`) для отображения данных о памяти.
 **Основные поля и их значения:**
	1. **MemTotal** :
	    - Общий объём физической оперативной памяти (RAM), доступный в системе.
	    - Это значение включает всю память, кроме зарезервированной ядром.
	2. **MemFree** :
	    - Количество свободной памяти, которая не используется в данный момент.
	    - Это "чистая" свободная память, которая может быть немедленно выделена процессам.
	3. **MemAvailable** :
	    - Оценка количества памяти, доступной для запуска новых приложений без свопирования.
	    - Учитывает кэшированную память и буферы, которые могут быть освобождены при необходимости.
	4. **Buffers** :
	    - Память, используемая для хранения метаданных файловой системы (например, inode-ов и блоков).
	5. **Cached** :
	    - Память, используемая для кэширования данных файловой системы. Эти данные могут быть освобождены при необходимости.
	6. **SwapCached** :
	    - Часть своп-памяти, которая была загружена обратно в оперативную память.
	7. **Active** :
	    - Память, активно используемая процессами.
	8. **Inactive** :
	    - Память, которая недавно использовалась, но в данный момент неактивна. Она может быть освобождена при необходимости.
	9. **SwapTotal** :
	    - Общий объём подкачки (swap space).
	10. **SwapFree** :
	    - Свободное место в подкачке.

**Команда `free -h`**
Команда `free` показывает краткую сводку о состоянии памяти в удобочитаемом формате. Флаг `-h` делает вывод более читаемым, добавляя единицы измерения (например, `G` для гигабайт, `M` для мегабайт).
 **Разбор столбцов команды free -h:**
	1. **total** :
	    - Общий объём памяти (соответствует `MemTotal` в `/proc/meminfo`).
	2. **used** :
	    - Используемая память. Это значение рассчитывается как:
	        used = total - free - buffers - cache
	3. **free** :
	    - Свободная память (соответствует `MemFree` в `/proc/meminfo`).
	4. **shared** :
	    - Память, используемая совместно несколькими процессами (например, tmpfs).
	5. **buff/cache** :
	    - Память, используемая для буферов (`Buffers`) и кэша (`Cached`).
	6. **available** :
	    - Доступная память (соответствует `MemAvailable` в `/proc/meminfo`).

 **Сравнение `/proc/meminfo` и `free -h`**

|**Параметр**|**/proc/meminfo**|**free -h**|
|---|---|---|
|**Общая память**|`MemTotal`|`total`|
|**Свободная память**|`MemFree`|`free`|
|**Доступная память**|`MemAvailable`|`available`|
|**Используемая память**|Рассчитывается как`MemTotal - MemFree - Buffers - Cached`|`used`|
|**Буферы**|`Buffers`|Входит в`buff/cache`|
|**Кэш**|`Cached`|Входит в`buff/cache`|
|**Подкачка (swap)**|`SwapTotal`,`SwapFree`|`Swap: total`,`Swap: free`|
**Основные различия:**
	1. **Уровень детализации:**
	    - `/proc/meminfo` предоставляет гораздо больше деталей, включая специфические метрики, такие как `Active`, `Inactive`, `SwapCached` и т.д.
	    - `free -h` показывает только основные метрики, такие как общая, свободная, используемая и доступная память.
	2. **Формат вывода:**
	    - `/proc/meminfo` — это текстовый файл с ключами и значениями, который требует ручного анализа.
	    - `free -h` — это готовый, удобочитаемый вывод, оптимизированный для быстрого просмотра.
	3. **Использование кэша и буферов:**
	    - В `/proc/meminfo` кэш и буферы разделены на отдельные поля (`Buffers` и `Cached`).
	    - В `free -h` они объединены в один столбец `buff/cache`.
	4. **Доступная память:**
	    - `/proc/meminfo` предоставляет поле `MemAvailable`, которое является точной оценкой доступной памяти.
	    - `free -h` также показывает `available`, но это значение берётся из `MemAvailable` в `/proc/meminfo`.

 **Зачем использовать оба инструмента?**
	- **`/proc/meminfo`:**
	       - Полезен для глубокого анализа состояния памяти.
	    - Подходит для скриптов или автоматизированных систем мониторинга.
	- **`free -h`:**
	    - Быстрый и удобный способ получить общее представление о состоянии памяти.
	    - Подходит для повседневного использования и быстрой диагностики.




## <span style="color: 4CAF50;">_Задание 5 Работа с системными вызовами</span>

Системные вызовы (system calls) — это интерфейс между пользовательским процессом и ядром операционной системы, механизмы, которые позволяют программам в пользовательском пространстве (user space) получать доступ к ресурсам и услугам, предоставляемым ядром операционной системы (kernel). Они являются основным способом взаимодействия между приложениями и ОС.

### <span style="color: 2196F3;">_1. Проанализируйте системные вызовы:</span>

●       Запустите strace ls и обратите внимание на вызовы open, close, read, write.

	 user@mypc  ~/myapp  strace -e trace=openat,close,read,write ls
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
	close(3)                                = 0
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libselinux.so.1", O_RDONLY|O_CLOEXEC) = 3
	read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832
	close(3)                                = 0
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
	read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\237\2\0\0\0\0\0"..., 832) = 832
	close(3)                                = 0
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpcre2-8.so.0", O_RDONLY|O_CLOEXEC) = 3
	read(3, "\177ELF\2\1\1\0\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0\0\0\0\0\0\0\0\0"..., 832) = 832
	close(3)                                = 0
	openat(AT_FDCWD, "/proc/filesystems", O_RDONLY|O_CLOEXEC) = 3
	read(3, "nodev\tsysfs\nnodev\ttmpfs\nnodev\tbd"..., 1024) = 455
	read(3, "", 1024)                       = 0
	close(3)                                = 0
	openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
	close(3)                                = 0
	openat(AT_FDCWD, ".", O_RDONLY|O_NONBLOCK|O_CLOEXEC|O_DIRECTORY) = 3
	close(3)                                = 0
	write(1, "mydaemon-package  mydaemon-packa"..., 39mydaemon-package  mydaemon-package.deb
	) = 39
	close(1)                                = 0
	close(2)                                = 0
	
	вывод команды strace:
	1. `openat` - это системный вызов для открытия файлов:
		- `/etc/ld.so.cache` - загрузчик библиотек читает кэш
		- `/lib/x86_64-linux-gnu/libselinux.so.1`, `/lib/x86_64-linux-gnu/libc.so.6`, `/lib/x86_64-linux-gnu/libpcre2-8.so.0` - загружаются необходимые динамические библиотеки
	2. `read` - чтение из открытых файлов:
		- Читается ELF-заголовок библиотек (первые 832 байта)
		- `/proc/filesystems` - информация о поддерживаемых файловых системах
	3. `close` - закрытие файлов после чтения
	4. Интересные моменты:
	    - `/usr/lib/locale/locale-archive` - локализация
		- `openat` с флагом `O_DIRECTORY` для текущей директории (`"."`) - подготовка к чтению содержимого директории
		- `write(1, ...)` - вывод результата работы команды ls в stdout
		- `close(1)` и `close(2)` - закрытие stdout и stderr перед завершением программы
	
	Вывод показывает, как команда ls:
	1. Загружает необходимые библиотеки
	2. Проверяет системную информацию
	3. Подготавливает локализацию
	4. Читает содержимое текущей директории
	5. Выводит результат на экран
	
	Все операции выполнены успешно, программа завершилась с кодом 0.	

### <span style="color: 2196F3;">_2. Проанализируйте работу конкретного процесса:</span>

●       Найдите PID процесса с помощью ps.
	запустим процесс в фоне 
	```
	  user@mypc  ~  ping google.com > output.log &
	[1] 50719
	 ⚙ user@mypc  ~ 
		```

●       Используйте strace -p [PID] для анализа его действий.
	
	```
	 ⚙ user@mypc  ~  sudo strace -e trace=write -p 50719
	[sudo] password for user:
	strace: Process 50719 attached
	write(1, "64 bytes from lj-in-f102.1e100.n"..., 87) = 87
	write(1, "64 bytes from lj-in-f102.1e100.n"..., 87) = 87
	write(1, "64 bytes from lj-in-f102.1e100.n"..., 87) = 87
	write(1, "64 bytes from lj-in-f102.1e100.n"..., 87) = 87
	```

### <span style="color: 2196F3;">_3. Отфильтруйте определенные вызовы:</span>

●       Используйте strace -e trace=open ls для отображения только операций открытия файлов.
	```
	user@mypc  ~/myapp  strace -e trace=openat ls
	openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libselinux.so.1", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libpcre2-8.so.0", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/proc/filesystems", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
	openat(AT_FDCWD, ".", O_RDONLY|O_NONBLOCK|O_CLOEXEC|O_DIRECTORY) = 3
	mydaemon-package  mydaemon-package.deb
	+++ exited with 0 +++
	```	

## <span style="color: 4CAF50;">_Задание 6 Демоны и сервисы</span>

### <span style="color: 2196F3;">_1. Создайте простой демон на Python и запустите его в фоне</span>

для более глубокого понимания как пишется код демона (двойной форк и управление ПИД файлом) код приведен без использования специальной библиотеки для написания демонов (daemonize)
```python
#!/usr/bin/python3  
import os  
import sys  
import time  
import signal  
import logging  
  
# Конфигурация  
LOG_FILE = '/var/log/demon/simple_daemon.log'  
PID_FILE = '/var/run/demon/simple_daemon.pid'  
WORK_INTERVAL = 5  # Интервал работы в секундах  
  
def configure_logging():  
    """Настройка логгирования."""  
    logging.basicConfig(  
        filename=LOG_FILE,  
        level=logging.INFO,  
        format='%(asctime)s - %(levelname)s - %(message)s'  
    )  
    return logging.getLogger()  
  
def daemonize():  
    """Основная логика демонизации (двойной форк и создание сессии)."""  
    # Первый форк  
    try:  
        pid = os.fork()  
        # Если pid > 0, значит мы находимся в родительском процессе, и он завершается (sys.exit(0)).  
        # Если pid == 0, значит мы находимся в дочернем процессе, который продолжает выполнение.        if pid > 0:  
            sys.exit(0)  # Завершаем родительский процесс  
    except OSError as e:  
        sys.stderr.write(f"Fork #1 failed: {e}\n")  
        sys.exit(1)  
  
    # Создаем новую сессию  
    os.setsid()  
    # Вызов os.setsid() создаёт новый сеанс и делает дочерний процесс лидером этого сеанса.  
    # Это гарантирует, что процесс больше не связан с управляющим терминалом.    os.umask(0)  
  
    # Второй форк  
    # Второй вызов os.fork() создаёт "внучатый" процесс.    # Первый дочерний процесс завершается (sys.exit(0)), а внучатый процесс продолжает выполнение.    # Это окончательно избавляет процесс от возможности стать лидером сеанса.    try:  
        pid = os.fork()  
        if pid > 0:  
            sys.exit(0)  # Завершаем первый дочерний процесс  
    except OSError as e:  
        sys.stderr.write(f"Fork #2 failed: {e}\n")  
        sys.exit(1)  
  
    # Перенаправляем стандартные потоки  
    sys.stdout.flush() # сбрасываем буфер стандартного вывода, отправляя все накопленные данные на экран или в файл  
    sys.stderr.flush()  
    with open('/dev/null', 'r') as f:  
        os.dup2(f.fileno(), sys.stdin.fileno())  
    with open(LOG_FILE, 'a+', encoding='utf-8') as stdout:  
        os.dup2(stdout.fileno(), sys.stdout.fileno())  
    with open(LOG_FILE, 'a+', encoding='utf-8') as stderr:  
        os.dup2(stderr.fileno(), sys.stderr.fileno())  
  
    # Записываем PID в файл  
    with open(PID_FILE, 'w') as f:  
        f.write(str(os.getpid()))  
  
def cleanup(signum, frame):  
    """Обработчик сигналов для очистки."""  
    logger = logging.getLogger()  
    if signum == signal.SIGTERM:  
        logger.info("Получен сигнал SIGTERM. Останавливаюсь...")  
    elif signum == signal.SIGINT:  
        logger.info("Получен сигнал SIGINT. Останавливаюсь...")  
    else:  
        logger.info(f"Получен сигнал {signum}. Останавливаюсь...")  
  
    if os.path.exists(PID_FILE):  
        os.remove(PID_FILE)  
    sys.exit(0)  
  
def run_daemon():  
    """Основная логика демона."""  
    logger = configure_logging()  
    logger.info("Демон запущен!")  
  
    # Регистрация обработчиков сигналов  
    signal.signal(signal.SIGTERM, cleanup)  
    signal.signal(signal.SIGINT, cleanup)  
  
    try:  
        while True:  
            logger.info("Демон работает...")  
            time.sleep(WORK_INTERVAL)  
    except Exception as e:  
        logger.error(f"Ошибка: {e}")  
        cleanup(None, None)  
  
if __name__ == "__main__":  
    # Проверка на уже запущенный демон  
    if os.path.exists(PID_FILE):  
        with open(PID_FILE, 'r') as f:  
            pid = int(f.read().strip())  
            if os.path.exists(f"/proc/{pid}"):  
                print("Демон уже запущен!")  
                sys.exit(1)  
            else:  
                os.remove(PID_FILE)  
  
    # Запуск демонизации  
    daemonize()  
    # Запуск основной логики  
    run_daemon()
```

сделаем файл исполняемым
`chmod +x demon.py

подготовим файл для логов
```bash
sudo touch /var/log/demon/simple_daemon.log
sudo chmod 644 /var/log/demon/simple_daemon.log
sudo chown user:user /var/log/demon/simple_daemon.log
```

сделаем отдельную папку для файла для хранения ПИДа демона
```
sudo mkdir /var/run/demon
sudo chown user:user /var/run/demon/
```

запустим демон
`python3 demon.py
или
`./demon.py


 смотрим лог 
```
user@mypc:~/demon$ tail -f /var/log/demon/simple_daemon.log
2025-02-11 00:07:58,327 - INFO - Демон работает...
2025-02-11 00:08:03,333 - INFO - Демон работает...
2025-02-11 00:08:08,338 - INFO - Демон работает...
2025-02-11 00:08:13,343 - INFO - Демон работает...
2025-02-11 00:08:18,349 - INFO - Демон работает...
```

пошлем демону сигнал SIGINT
`kill -2 20695

смотрим лог 
```
user@mypc:~/demon$ tail -f /var/log/demon/simple_daemon.log
2025-02-11 00:08:38,370 - INFO - Демон работает...
2025-02-11 00:08:38,586 - INFO - Получен сигнал SIGINT. Останавливаюсь...
```



### <span style="color: 2196F3;">_2. Напишите unit-файл для systemd и проверьте его работу через systemctl</span>

1. Сделаем с использованием отдельного пользователя, специально для запуска демона. Это позволит изолировать демон от других процессов и ограничить его права доступа.
	`sudo useradd -r -s /bin/false demonuser
	- `-r`: Создаёт системного пользователя (без домашней директории).
	- `-s /bin/false`: Задаёт оболочку `/bin/false`, чтобы пользователь не мог войти в систему.
2. Настройка прав доступа
	Добавьте пользователя `demonuser` в группу владельца файла
		Чтобы предоставить доступ к файлу демона, добавьте пользователя `demonuser` в группу `user`. По лучшей практике надо сделать отдельную группу для демона и туда добавить user и demonuser.
		`sudo usermod -aG user demonuser
	Убедитесь, что `demonuser` добавлен в группу:
		`groups demonuser
	
	 Измените права доступа к файлу и директории
		1. **Директория `/home/user/demon/`** : Убедитесь, что группа имеет права на чтение и выполнение:
	  	    `chmod 750 /home/user/demon/
	      Пример корректных прав:
	    `user@mypc:~$ ls -ld /home/user/demon/
		`drwxr-x--- 4 user user 4096 фев  9 08:06 /home/user/demon/

	    
	 **Файл `/home/user/demon/demon.py`** : Убедитесь, что группа имеет права на чтение и выполнение:
	    `chmod 750 /home/user/demon/demon.py
	    Пример корректных прав:
		`user@mypc:~$ ls -l /home/user/demon/demon.py
		`-rwxr-x--- 1 user user 4622 фев 11 21:04 /home/user/demon/demon.py

	Проверьте права на папку и файлы:
		```
			user@mypc:~$ ls -ld /var/log/demon/
			drwxr-xr-x 2 user user 4096 фев 11 21:05 /var/log/demon/

			user@mypc:~$ ls -l /var/log/demon/simple_daemon.log
			-rw-rw-rw- 1 user user 6145 фев 11 21:42 /var/log/demon/simple_daemon.log
		```


3. Тестирование запуска демона
	Попробуйте запустить демон от имени `demonuser`:
	`sudo -u demonuser /usr/bin/python3 /home/user/demon/demon.py
	Если возникает ошибка:
	1. Проверьте вывод ошибки.
	2. Убедитесь, что все зависимости скрипта установлены.
	3. Проверьте логи системы:
	    `sudo journalctl -xe


4. Настройка службы systemd
		Если демон должен запускаться автоматически через `systemd`, создайте unit-файл.
		#### Пример unit-файла (`/etc/systemd/system/simple_daemon.service`):
			```
				[Unit]
				Description=Simple Python Daemon
				After=network.target
				
				[Service]
				Type=simple
				User=demonuser
				Group=user
				
				#ExecStartPre=/bin/mkdir -p /var/demon 
				#ExecStartPre=/bin/chown demonuser:user /var/demon
				RuntimeDirectory=demon
				PIDFile=/var/run/demon/simple_daemon.pid
				
				WorkingDirectory=/home/user/demon
				ExecStart=/usr/bin/python3 /home/user/demon/demon.py
				Restart=on-failure
				RestartSec=10

				
				[Install]
				WantedBy=multi-user.target
			```	
.


 1. Команды для управления службой:
	1. Перечитайте конфигурацию:
	    `sudo systemctl daemon-reload
	    
	2. Запустите службу:
	    `sudo systemctl start simple_daemon.service
	    
	3. Добавьте службу в автозагрузку:
	    `sudo systemctl enable simple_daemon.service
	    
	4. Проверьте статус службы:
	    `sudo systemctl status simple_daemon.service

Проверка логов системы
	`sudo journalctl -u simple_daemon.service




### <span style="color: 2196F3;">_3. Настройте ротацию логов:</span>
●       Настройте logrotate для своего демона с настройкой ротации раз в неделю.

**Как настроить logrotate?**
1. Основной конфигурационный файл:
	- Расположение: `/etc/logrotate.conf`.
	- Содержит глобальные настройки и подключает конфиги из `/etc/logrotate.d/`.   

2. Пользовательские конфиги:
	- Создавайте файлы в `/etc/logrotate.d/` (например, `/etc/logrotate.d/simple_daemon`).


Пример настройки ротации логов для нашего демона:
```
/var/log/demon/*.log {
        weekly         # частота ротаии - раз в неделю
        rotate 30      # хранить 30 архивов
        compress       # использовать сжатие
        delaycompress  # последний архивированный лог не сжимать
        notifempty     # пустые логи не 
        create 0664 user user    # создать новый лог с указанными правами
        sharedscripts    # выполнить скрипт только один раз для всех логов
        postrotate       # скрипт выполняемый после ротации
                systemctl restart simple_daemon.service
        endscript
}
```

Проверка и отладка
1. **Проверка синтаксиса** (без выполнения):
    `sudo logrotate -d /etc/logrotate.d/myapp
    
2. **Принудительный запуск**:
	`sudo logrotate -vf /etc/logrotate.d/myapp

Частые ошибки
- **Ошибка прав доступа**: Убедитесь, что после ротации создается файл с правильным владельцем (`create`).
- **Скрипты не выполняются**: Проверьте синтаксис внутри `postrotate`/`prerotate`.
- **Логи не ротируются**: Убедитесь, что файл конфига находится в `/etc/logrotate.d/` и имеет права `644`.





## <span style="color: 4CAF50;">_Задание 7 Стресс-тестирование системы</span>
### <span style="color: 2196F3;">_1. Создайте нагрузку на CPU при помощи утилиты и проанализируйте данные в top или htop</span>

Запустим утилиту stress с нагрузкой на CPU
`stress --cpu $(nproc)

Запустим top
После запуска `top` вы увидите динамически обновляющийся список процессов. Обратите внимание на следующие колонки, чтобы проанализировать нагрузку на CPU:
- **%Cpu(s):** В верхней части экрана вы увидите сводку использования CPU.
    - **us (user):** Процент CPU времени, потраченного на процессы пользователя. Это время, которое CPU тратит на выполнение кода приложений. Когда `stress` создает нагрузку, это значение должно увеличиться.
    - **sy (system):** Процент CPU времени, потраченного на системные процессы (ядро). Может немного увеличиться, но обычно не так значительно, как `us` при нагрузке от `stress`.
    - **ni (nice)**: Процент CPU времени, потраченного на процессы, запущенные с измененным приоритетом в сторону **пользовательской настройки "nice"**
    - **id (idle):** Процент CPU времени простоя. При высокой нагрузке это значение должно уменьшиться.
    - **wa (wait):** Процент CPU времени, потраченного в ожидании ввода/вывода. Обычно не сильно влияет при CPU-интенсивной нагрузке.
    - **hi (hardware interrupts):** Процент CPU времени, потраченного на обработку аппаратных прерываний.
    - **si (software interrupts):** Процент CPU времени, потраченного на обработку программных прерываний.
    - **st (steal time):** Процент времени, украденного у виртуальной машины гипервизором для других виртуальных машин.
- **PID:** Идентификатор процесса.
- **USER:** Имя пользователя, которому принадлежит процесс.
- **%CPU:** Процент использования CPU процессом. Вы увидите процессы `stress` в этом списке, и их `%CPU` значение должно быть высоким.
- **COMMAND:** Команда, выполняемая процессом. Вы увидите процессы `stress`.

```
user@mypc:~$ top
top - 00:28:48 up 57 min,  2 users,  load average: 2,98, 2,15, 1,42
Tasks: 253 total,   4 running, 249 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0,1 us,  0,2 sy, 75,0 ni, 24,7 id,  0,0 wa,  0,0 hi,  0,0 si,  0,0 st
MiB Mem :   7941,7 total,   6710,1 free,    540,0 used,    691,6 buff/cache
MiB Swap:   2048,0 total,   2048,0 free,      0,0 used.   7153,1 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  11175 user      30  10    3708    256    256 R 100,0   0,0   5:04.46 stress
  11177 user      30  10    3708    256    256 R 100,0   0,0   5:04.40 stress
  11176 user      30  10    3708    256    256 R  99,7   0,0   5:04.46 stress
  11214 user      20   0    9976   3584   3328 S   0,3   0,0   0:00.39 bash
      1 root      20   0  167340  12124   8156 S   0,0   0,1   0:01.61 systemd
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.00 kthreadd
      3 root      20   0       0      0      0 S   0,0   0,0   0:00.00 pool_work+
      4 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R+
      5 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R+
      6 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R+
      7 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/R+
      9 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 kworker/0+
     10 root      20   0       0      0      0 I   0,0   0,0   0:00.13 kworker/0+

```


`htop` - это интерактивный просмотрщик процессов, который является более продвинутой альтернативой `top`. Он предоставляет более удобный интерфейс, цветную подсветку, и позволяет более гибко управлять процессами.



### <span style="color: 2196F3;">_2. Создайте нагрузку на память при помощи утилиты и проанализируйте данные в top или htop</span>

Для создания нагрузки на память мы снова будем использовать утилиту `stress`, так как она предоставляет гибкие возможности для имитации различных видов системной нагрузки.
Утилита `stress` позволяет создавать нагрузку на память с помощью опции `--vm` (virtual memory). Эта опция заставляет `stress` выделить указанное количество виртуальной памяти и затем "трогать" (touch) эту память, заставляя систему реально выделять физическую память и использовать ее.
Вот команда для создания нагрузки на память:
```
stress --vm 1 --vm-bytes 1G --timeout 60s
```
Разберем опции в этой команде:
- `--vm 1`: Указывает `stress` запустить 1 процесс, который будет выделять память. Вы можете увеличить это число, чтобы создать более интенсивную нагрузку, запустив несколько процессов, выделяющих память одновременно. Например, `--vm 4` запустит 4 процесса.
- `--vm-bytes 1G`: Указывает, сколько памяти должен выделить каждый процесс. В данном случае `1G` означает 1 Гигабайт. Вы можете изменить это значение, например, на `500M` для 500 Мегабайт или `2G` для 2 Гигабайт, в зависимости от того, какую нагрузку вы хотите создать и сколько памяти доступно в вашей системе. **Важно!** Не выделяйте слишком много памяти, особенно если у вас мало оперативной памяти и/или нет файла подкачки/раздела swap. Это может привести к замедлению системы или даже к ее зависанию, если система начнет активно использовать swap или исчерпает память. Начните с умеренных значений и постепенно увеличивайте, если необходимо.
- `--timeout 60s`: Устанавливает время работы `stress` в 60 секунд (1 минута). Это удобно, чтобы нагрузка не продолжалась бесконечно. Вы можете убрать эту опцию, чтобы `stress` работал, пока вы его не остановите вручную, или изменить время, например, `--timeout 300s` для 5 минут.

**Дополнительные параметры `stress` для нагрузки на память:**
- `--vm-keep`: По умолчанию, `stress` освобождает выделенную память перед выходом. Опция `--vm-keep` заставляет процессы `stress` удерживать выделенную память до завершения, что может усилить нагрузку.
- `--vm-hang <минуты>`: Заставляет процессы `stress` "засыпать" на указанное количество минут после выделения памяти.

обратите внимание на следующие параметры в выводе `top`:
- **Строка `Mem:` (Информация об использовании памяти):**
    - **total:** Общее количество оперативной памяти (RAM) в системе.
    - **free:** Количество свободной оперативной памяти. При нагрузке на память это значение должно уменьшиться.
    - **used:** Количество используемой оперативной памяти. Это значение должно увеличиться, когда `stress` выделяет память.
    - **buff/cache:** Память, используемая для буферов и кэша файловой системы. Система может использовать часть свободной памяти для кэширования, чтобы ускорить доступ к файлам. Это значение может меняться, но обычно не так критично для анализа нагрузки на память, как `free` и `used`.
    - **available (не всегда отображается, зависит от версии `top`):** Оценка количества памяти, доступной для запуска новых приложений, без использования swap.
- **Строка `Swap:` (Информация об использовании swap-пространства):**
    - **total:** Общий размер swap-пространства (файла подкачки или раздела swap).
    - **free:** Количество свободного swap-пространства.
    - **used:** Количество используемого swap-пространства. Если физической памяти начинает не хватать, система может начать использовать swap для "выгрузки" менее активных страниц памяти на диск. Увеличение `used` в swap указывает на нехватку оперативной памяти и начало использования swap, что может замедлить систему.
- **Колонка `%MEM` в списке процессов:**
    - Для каждого процесса, колонка `%MEM` показывает процент оперативной памяти, используемой этим процессом, относительно общего объема оперативной памяти.
    - Вы должны увидеть процесс `stress` (или несколько процессов, если вы запустили `stress` с `--vm` больше 1) в списке процессов, и его значение `%MEM` должно быть заметным, особенно если вы выделили значительное количество памяти.
- **Колонки `VIRT`, `RES`, `SHR` для процессов:**
    - **VIRT (Virtual Memory Size):** Общий объем виртуальной памяти, выделенной процессом. Это включает в себя код, данные, разделяемые библиотеки, а также память, выделенную, но еще не обязательно физически присутствующую в RAM (например, memory-mapped files, выделенная, но не использованная память). Для процесса `stress`, использующего `--vm`, значение `VIRT` будет примерно соответствовать объему памяти, который вы указали в `--vm-bytes`.
    - **RES (Resident Memory Size):** Фактический объем физической оперативной памяти, используемой процессом. Это память, которая реально находится в RAM. Для процесса `stress`, значение `RES` будет увеличиваться по мере того, как процесс "трогает" выделенную виртуальную память и система выделяет ему физическую память.
    - **SHR (Shared Memory Size):** Объем разделяемой памяти, используемой процессом. Обычно для нагрузки, создаваемой `stress --vm`, это значение не будет существенно меняться.

**Пример вывода `top` во время работы `stress` (примерные значения):**
```
top - 16:45:00 up 1 day, 3:49,  1 user,  load average: 0.50, 0.30, 0.20
Tasks: 200 total,   1 running, 199 sleeping,   0 stopped,   0 zombie
%Cpu(s):  5.0 us,  1.0 sy,  0.0 ni, 94.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  8000000 total,  1000000 free,  6000000 used,  1000000 buff/cache
KiB Swap:  2000000 total,  2000000 free,        0 used.  2000000 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 1234 user      20   0  1048m  1020m   4m R  2.0 12.7   0:05.00 stress
```
В этом примере (значения приблизительные и зависят от системы):
- **`Mem:`** показывает, что из 8GB общей памяти, 6GB уже используется (`used`), и только 1GB свободен (`free`). Это меньше, чем было до запуска `stress`.
- **`Swap:`** пока не используется (`used` = 0), что хорошо. Если бы `Swap: used` начало расти, это означало бы, что система испытывает дефицит оперативной памяти.
- Процесс `stress` (PID 1234) имеет `VIRT` около 1GB (1048m), что соответствует `vm-bytes 1G` в нашей команде. `RES` (1020m) также близко к 1GB, что означает, что процесс реально использует около 1GB оперативной памяти. `%MEM` показывает 12.7%, что является значительной долей от общей памяти в 8GB.



В `htop` мониторинг памяти еще более нагляден:
- **Memory Bar:** В верхней части `htop` есть графическая шкала, показывающая использование оперативной памяти. Разные цвета могут обозначать разные типы использования памяти (например, used, buffers, cache). Вы увидите, как заполняется шкала `Mem` по мере работы `stress`.
- **Swap Bar:** Ниже шкалы `Mem` находится шкала `Swp` (Swap), показывающая использование swap-пространства. Следите за ней, чтобы увидеть, не начала ли система использовать swap из-за нехватки RAM.
- **Processes List:** Список процессов в `htop` также содержит колонки `%MEM`, `VIRT`, `RES`, `SHR`, как и в `top`. В `htop` эти колонки могут быть цветными и более удобно расположены для визуального анализа.


### <span style="color: 2196F3;">_3. Создайте нагрузку на диск при помощи утилиты и проанализируйте данные в top или htop</span>

Утилита `stress` предоставляет опцию `--io` для создания нагрузки на подсистему ввода/вывода. Эта опция заставляет `stress` запускать процессы, которые выполняют операции синхронной записи и чтения данных на диск.

Используйте следующую команду для создания нагрузки на диск:
```bash
stress --io 4 --hdd-bytes 2G --timeout 60s
```
Разберем параметры этой команды:
- `--io 4`: Указывает `stress` запустить 4 процесса, которые будут выполнять операции ввода/вывода на диск. Вы можете изменить это число, чтобы увеличить или уменьшить интенсивность нагрузки. Например, `--io 2` запустит 2 процесса, а `--io 8` - 8 процессов, создающих I/O нагрузку. Количество процессов `--io` не обязательно должно совпадать с количеством CPU ядер, в отличие от опции `--cpu`.
- `--hdd-bytes 2G`: Указывает общий объем данных, который процессы `stress` будут пытаться записать на диск. В данном случае `2G` означает 2 Гигабайта. Каждый процесс из `--io` будет пытаться записать часть этого общего объема. Вы можете изменить это значение, например, на `1G`, `5G`, или даже использовать значения в мегабайтах (например, `500M`). **Важно:** Убедитесь, что на вашем диске есть достаточно свободного места для записи указанного объема данных. Если свободное место ограничено, или вы укажете слишком большой объем, это может привести к замедлению работы системы или заполнению диска.
- `--timeout 60s`: Устанавливает время работы `stress` в 60 секунд (1 минута). Это ограничивает продолжительность нагрузки. Вы можете убрать эту опцию, чтобы `stress` работал, пока вы не остановите его вручную, или увеличить время, например, `--timeout 300s` для 5 минут.

**Дополнительные параметры `stress` для нагрузки на диск:**
- `--file <количество>`: Указывает количество файлов, которые будут использоваться для операций ввода/вывода. По умолчанию `stress` использует 1 файл на процесс `--io`. Увеличение количества файлов может повлиять на характер нагрузки, например, имитировать работу с множеством файлов.
- `--timeout <секунды>`: Как и ранее, для ограничения времени работы `stress`.
- `--verbose`: Для получения более подробной информации о работе `stress`.



В выводе `top` для анализа нагрузки на диск обратите внимание на следующие параметры:
- **Строка `%Cpu(s):` (Информация об использовании CPU):**
    - **wa (wait):** **Это ключевой параметр для анализа дисковой нагрузки.** `%wa` показывает процент CPU времени, которое процессор простаивал, ожидая завершения операций ввода/вывода (I/O). Когда процессы активно выполняют дисковые операции, CPU может простаивать, ожидая, пока данные будут прочитаны с диска или записаны на диск. При интенсивной дисковой нагрузке значение `%wa` должно заметно увеличиться. Высокий `%wa` (например, 20% и выше) часто указывает на то, что дисковая подсистема является "узким местом" и ограничивает производительность системы.
- **Строка `KiB Mem` и `KiB Swap` (Информация об использовании памяти и swap):** Хотя мы создаем нагрузку на диск, а не на память, мониторинг использования памяти и swap также может быть полезен. Интенсивный ввод/вывод может быть связан с активным использованием памяти (например, чтение больших файлов, кэширование дисковых операций). Обратите внимание на значения `free` и `used` в строках `Mem` и `Swap`, как мы делали в примере с нагрузкой на память. Если система начинает активно использовать swap при дисковой нагрузке, это может указывать на то, что операции ввода/вывода вызывают нехватку оперативной памяти, и система пытается компенсировать ее использованием диска в качестве swap.
- **Список процессов и колонка `%CPU`:** Посмотрите на список процессов. Процессы `stress`, которые мы запустили для создания I/O нагрузки, могут не показывать очень высокое использование `%CPU`, особенно если дисковая подсистема медленная. Основное время они будут проводить в ожидании ввода/вывода. Однако, если какие-то другие процессы также активно участвуют в дисковых операциях, они могут появиться в списке `top`.
- **Дополнительные колонки (опционально):** В некоторых версиях `top` (или с определенными настройками) можно отобразить колонки, непосредственно показывающие дисковый ввод/вывод, такие как "DISK READ" (RDDSK), "DISK WRITE" (WRDSK), "IO WAIT" (IOWAIT). Чтобы добавить эти колонки в `top`, можно нажать клавишу `o` (затем `Поле`) и выбрать нужные параметры из списка, например, `IO_WAIT`, `RDDSK`, `WRDSK`. Это может предоставить более прямую информацию о дисковой активности процессов. **Обратите внимание:** доступность этих колонок и способ их добавления может зависеть от версии `top`.

**Пример вывода `top` во время работы `stress` (примерные значения):**
```
top - 17:30:00 up 1 day, 4:34,  1 user,  load average: 1.00, 0.80, 0.50
Tasks: 200 total,   5 running, 195 sleeping,   0 stopped,   0 zombie
%Cpu(s):  10.0 us,  2.0 sy,  0.0 ni,  68.0 id, **20.0 wa**,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  8000000 total,  4000000 free,  3000000 used,  1000000 buff/cache
KiB Swap:  2000000 total,  2000000 free,        0 used.  2000000 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 1234 user      20   0   100m   5m   4m D  0.3  0.1   0:05.00 stress
 1235 user      20   0   100m   5m   4m D  0.2  0.1   0:04.50 stress
 1236 user      20   0   100m   5m   4m D  0.2  0.1   0:04.00 stress
 1237 user      20   0   100m   5m   4m D  0.2  0.1   0:03.50 stress
 ... (и так далее, до 4 процессов stress, в соответствии с --io 4)
```
В этом примере (значения приблизительные):
- **`%Cpu(s):`** показывает `20.0 wa`, что означает 20% CPU времени процессор простаивал, ожидая ввода/вывода. Это указывает на значительную дисковую нагрузку. Значения `%us` и `%sy` относительно невелики, что типично для I/O-связанных нагрузок.
- Процессы `stress` в колонке `COMMAND` имеют статус `D` (Uninterruptible sleep - часто состояние ожидания I/O). Их `%CPU` использование очень низкое (около 0.2-0.3%), что также характерно, так как большую часть времени они проводят в ожидании завершения дисковых операций.


В `htop` мониторинг дисковой нагрузки также становится более наглядным:
- **CPU Usage Bars:** В верхней части `htop` вы увидите графические полосы загрузки CPU ядер. В случае дисковой нагрузки, вы можете заметить, что часть полосы, представляющая **I/O Wait** (обычно обозначается другим цветом, например, желтым или оранжевым), увеличивается. Это графическое представление параметра `%wa` из `top`.
- **Processes List:** Список процессов в `htop` аналогичен `top`, но может быть более информативным. Вы также увидите процессы `stress`. Чтобы добавить колонки, показывающие дисковый ввод/вывод, в `htop`, вы можете нажать `F6` (Sort by), и в списке выбрать параметры, связанные с I/O, например, `READ_RATE`, `WRITE_RATE`, `IO_READ`, `IO_WRITE`. Это позволит вам отсортировать процессы по интенсивности их дисковых операций и увидеть, какие процессы вносят наибольший вклад в нагрузку. **Обратите внимание:** доступность этих колонок по умолчанию может зависеть от версии `htop`.

**Анализ результатов**
- **Высокое значение `%wa` в `top` или заметная часть I/O Wait в графиках CPU в `htop`:** Это основной признак того, что система испытывает дисковую нагрузку и процессор простаивает в ожидании операций ввода/вывода. Чем выше `%wa`, тем сильнее выражена дисковая "узкое место".
- **Процессы `stress` в списке процессов (имеющие статус `D` в `top` или высокий `READ_RATE`/`WRITE_RATE` в `htop`, если добавлены соответствующие колонки):** Подтверждает, что именно процессы `stress` являются источником дисковой нагрузки.
- **Относительно низкое значение `%CPU` для процессов `stress`:** Типично для I/O-связанных нагрузок. Процессы проводят большую часть времени в ожидании, а не активно используя CPU для вычислений.
- **Мониторинг использования памяти и swap (строки `Mem:` и `Swap:` в `top` или шкалы `Mem` и `Swp` в `htop`):** Позволяет убедиться, что дисковая нагрузка не приводит к нехватке памяти и активному использованию swap, что могло бы еще больше замедлить систему.


## <span style="color: 4CAF50;">_Задание 8 Траблшутинг</span>

### <span style="color: 2196F3;">1. Решите следующие задачи</span>

https://sadservers.com/scenario/saskatoon

cut -d ' ' -f1 /home/admin/access.log | sort | uniq -c | sort -nr | head -n 1 | awk '{print $2}' > /home/admin/highestip.txt 
sha1sum /home/admin/highestip.txt


https://sadservers.com/scenario/santiago этой задачи на сервере нет, ИИ подобрал другую задачу со схожей тематикой https://sadservers.com/scenario/lhasa

printf "%.2f\n" $(echo "scale=4; $(awk '{sum+=$2} END {print sum/NR}' /home/admin/scores.txt)" | bc) > /home/admin/solution