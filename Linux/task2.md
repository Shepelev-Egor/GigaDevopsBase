# <span style="color: #ff5733;">_РАЗДЕЛ 2 Основы работы в терминале</span>

## <span style="color: #4CAF50;">_Задание 1</span>

1.     В домашней директории создать директорию проекта (project)
mkdir project

2.     Внутри создать следующую структуру директорий:

   project/

├── src/  
├── docs/  
├── config/  
├── logs/  
└── backups/
cd project/
mkdir src docs config logs backups



3.     Создать текстовые файлы в папке src, например main.py и занести какой-то текст в него.
user@mypc:~/project$ echo "print('Hello world')" > src/main.py
user@mypc:~/project$ cat src/main.py
print('Hello world')


4.     Переместить файл в другую директорию (например, docs).
mv src/main.py docs/

5.     Скопировать файл обратно в src:
cp docs/main.py src/


## <span style="color: #4CAF50; #4CAF50">_Задание 2</span>

**Требования**

1.     Создать группу developers и добавить текущего пользователя.

Проверяем в каких группах состоит пользователь:
user@mypc:~/project$ groups $user
user adm cdrom sudo dip plugdev lpadmin lxd sambashare
или
user@mypc:~/project$ id $USER
uid=1000(user) gid=1000(user) groups=1000(user),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),122(lpadmin),135(lxd),136(sambashare)

Создаем группу developers
sudo groupadd developers

Просмотр всех групп
cat /etc/group
...
...
...
geoclue:x:131:
pulse:x:132:
pulse-access:x:133:
gdm:x:134:
lxd:x:135:user
user:x:1000:
sambashare:x:136:user
rdma:x:137:
user1:x:1001:
developers:x:1002:

Добавляем пользователя в группу developers
user@mypc:~/project$ sudo usermod -aG developers $USER

Чтобы изменения вступили в силу, нужно выйти и войти в оболочку заново и после проверить:
user@mypc:~$ id
uid=1000(user) gid=1000(user) groups=1000(user),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),122(lpadmin),135(lxd),136(sambashare),1002(developers)



2.     Установить различные права доступа:

●       Для src/ – **770**, а также добавить во владельцев группу developers.
проверить права до изменения
user@mypc:~/project$ ls -la
total 28
drwxrwxr-x  7 user user 4096 янв  9 23:22 .
drwxr-x--- 19 user user 4096 янв  9 23:52 ..
drwxrwxr-x  2 user user 4096 янв  9 23:22 backups
drwxrwxr-x  2 user user 4096 янв  9 23:22 config
drwxrwxr-x  2 user user 4096 янв  9 23:37 docs
drwxrwxr-x  2 user user 4096 янв  9 23:22 logs
drwxrwxr-x  2 user user 4096 янв  9 23:38 src
user@mypc:~/project$

user@mypc:~/project$ chmod 770 src/
user@mypc:~/project$ ls -la
total 28
drwxrwxr-x  7 user user 4096 янв  9 23:22 .
drwxr-x--- 19 user user 4096 янв  9 23:52 ..
drwxrwxr-x  2 user user 4096 янв  9 23:22 backups
drwxrwxr-x  2 user user 4096 янв  9 23:22 config
drwxrwxr-x  2 user user 4096 янв  9 23:37 docs
drwxrwxr-x  2 user user 4096 янв  9 23:22 logs
drwxrwx---  2 user user 4096 янв  9 23:38 src

user@mypc:~/project$ chown user:developers src/
user@mypc:~/project$ ls -la
total 28
drwxrwxr-x  7 user user       4096 янв  9 23:22 .
drwxr-x--- 19 user user       4096 янв  9 23:52 ..
drwxrwxr-x  2 user user       4096 янв  9 23:22 backups
drwxrwxr-x  2 user user       4096 янв  9 23:22 config
drwxrwxr-x  2 user user       4096 янв  9 23:37 docs
drwxrwxr-x  2 user user       4096 янв  9 23:22 logs
drwxrwx---  2 user developers 4096 янв  9 23:38 src




●       Для logs/ – **750**.
user@mypc:~/project$ chmod 750 logs/
user@mypc:~/project$ ls -la
total 28
drwxrwxr-x  7 user user       4096 янв  9 23:22 .
drwxr-x--- 19 user user       4096 янв  9 23:52 ..
drwxrwxr-x  2 user user       4096 янв  9 23:22 backups
drwxrwxr-x  2 user user       4096 янв  9 23:22 config
drwxrwxr-x  2 user user       4096 янв  9 23:37 docs
drwxr-x---  2 user user       4096 янв  9 23:22 logs
drwxrwx---  2 user developers 4096 янв  9 23:38 src


●       Для config/ – **750**, также установить **sticky** **bit**
**Символьный формат**:
Для добавления sticky bit используйте команду:
   chmod +t имя_каталога
Для удаления sticky bit:
   chmod -t имя_каталога
user@mypc:~/project$ chmod +t config/
user@mypc:~/project$ ls -la
total 28
drwxrwxr-x  7 user user       4096 янв  9 23:22 .
drwxr-x--- 19 user user       4096 янв  9 23:52 ..
drwxrwxr-x  2 user user       4096 янв  9 23:22 backups
drwxrwxr-t  2 user user       4096 янв  9 23:22 config
drwxrwxr-x  2 user user       4096 янв  9 23:37 docs
drwxr-x---  2 user user       4096 янв  9 23:22 logs
drwxrwx---  2 user developers 4096 янв  9 23:38 src



●       Для backups/ – **700**, установить владельца root  
user@mypc:~/project$ sudo chown root backups/
[sudo] password for user:
user@mypc:~/project$ ls -la
total 28
drwxrwxr-x  7 user user       4096 янв  9 23:22 .
drwxr-x--- 19 user user       4096 янв  9 23:52 ..
drwxrwxr-x  2 root user       4096 янв  9 23:22 backups
drwxrwxr-t  2 user user       4096 янв  9 23:22 config
drwxrwxr-x  2 user user       4096 янв  9 23:37 docs
drwxr-x---  2 user user       4096 янв  9 23:22 logs
drwxrwx---  2 user developers 4096 янв  9 23:38 src

3.     Проверить правильность прав
user@mypc:~/project$ ls -la
total 28
drwxrwxr-x  7 user user       4096 янв  9 23:22 .
drwxr-x--- 19 user user       4096 янв  9 23:52 ..
drwxrwxr-x  2 root user       4096 янв  9 23:22 backups
drwxrwxr-t  2 user user       4096 янв  9 23:22 config
drwxrwxr-x  2 user user       4096 янв  9 23:37 docs
drwxr-x---  2 user user       4096 янв  9 23:22 logs
drwxrwx---  2 user developers 4096 янв  9 23:38 src


## <span style="color: #4CAF50; ">_Задание 3</span>

1.     Найти все файлы, измененные за последние 24 часа:
user@mypc:/home$ find -mmin -1440
./user
./user/.bash_history 
./user/.Xauthority
./user/project
./user/project/docs
./user/project/docs/main.py
./user/project/config
./user/project/src
./user/project/src/main.py
./user/project/backups
./user/project/logs
./user/snap/snapd-desktop-integration/253/.config/ibus
./user/snap/snapd-desktop-integration/253/.config/ibus/bus
./user/.oh-my-zsh/cache/grep-alias
./user/.oh-my-zsh/log
./user/.zsh_history
./user/.config/pulse/b4aa0c17b6e346e2b60a8affd2b7ccd1-default-source
./user/.config/pulse/b4aa0c17b6e346e2b60a8affd2b7ccd1-default-sink
user@mypc:/home$


2.     Найти файл main.py при помощи команды grep, по тексту, который в нем содержится.
user@mypc:~/project/src$ grep -r "Hello world" /home
/home/user/.bash_history:echo "print('Hello world')" > src/main.py
/home/user/.bash_history:echo "print('Hello world')" > src/main.py
/home/user/project/docs/main.py:print('Hello world')
/home/user/project/src/main.py:print('Hello world')

3.     Показать статистику по типам файлов в домашней директории.

## <span style="color: #4CAF50;">_Задание 4</span>

Используйте редактор vim

---
1. **Создать новый текстовый файл**

- Откройте терминал и введите:
    vim filename.txt
    (где `filename.txt` — имя вашего файла).

---
2. Записать какой-то текст, который содержит несколько строк.

- Нажмите **`i`** (выйдете в режим вставки).
- Введите текст, например: 
    Это первая строка.
    Это вторая строка.
    Это третья строка.
- Нажмите **Enter** , чтобы перейти на новую строку.
- Чтобы выйти из режима вставки, нажмите **Esc** .

---
 3. **Сохранить и выйти**

- В командном режиме (после нажатия **Esc** ) введите:
    :wq
    (`:w` — сохранить, `:q` — выйти). Нажмите **Enter** .

---
 4. **Перейти к нужной строке**

- Откройте файл снова:
    vim filename.txt
- В командном режиме введите номер строки и нажмите **Enter** :
    :2
    (перейдет ко **второй** строке).  
    Для третьей строки: `:3`.

---
 5. **Удалить строку**

- Убедитесь, что курсор находится на нужной строке (например, на второй).
- Нажмите **`dd`** — строка будет удалена.
- Чтобы вернуть удаленную строку, нажмите **`u`** (отмена).

---
 6. **Включить отображение номеров строк**
- В командном режиме введите:
    :set number
    Номера строк появятся в левом краю.  
Чтобы отключить:
	`:set nonumber`.

---
 7. **Выполнить поиск по тексту**

- В командном режиме введите:
    /текст_для_поиска
    Например, `/строка` найдет все вхождения слова "строка".
- Нажмите **n** , чтобы перейти к следующему совпадению, или **N** — к предыдущему.

---
 8. **Исследовать другие возможности Vim**

- **Перемещение по файлу:**
    - `h` — влево, `l` — вправо, `j` — вниз, `k` — вверх.
    - `gg` — в начало файла, `G` — в конец.
- **Редактирование:**
    - `x` — удалить символ под курсором.
    - `yy` — копировать строку, `p` — вставить после курсора.
    - `u` — отменить последнее действие, `Ctrl+R` — повторить.
- **Замена текста:**
    - В командном режиме:
        :%s/старое/новое/g
        (заменит все вхождения "старое" на "новое").
- **Визуальный режим:**
    - Нажмите **`v`** , затем выделите текст, и используйте `d` для удаления, `y` для копирования.
- **Плагины и расширения:**
    - Установите плагин-менеджер (например, **Vundle** ) для расширения функционала.




РЕЖИМЫ работы vim
```
1 Normal Mode h/j/k/l d/y/p//
2 ├── i/a/I/A/o/O → Insert Mode
3 ├── v/V → Visual Mode
4 ├── R → Replace Mode
5 ├── : → Command-line Mode
6 └── gh → Select Mode
```
---
 1. **Нормальный режим (Normal Mode)**

- **Начальный режим** при запуске Vim.
- **Что можно делать:**
    - Перемещаться по файлу: `h` (влево), `j` (вниз), `k` (вверх), `l` (вправо).
    - Выполнять действия: удаление (`d`), копирование (`y`), вставка (`p`), поиск (`/`).
    - Переходить в другие режимы (вставка, командный).
- **Как попасть сюда:** Нажмите **`Esc`** (важно!).

---
 2. **Режим вставки (Insert Mode)**

- Режим для **ввода текста** .
- **Как активировать:**
    - `i` — вставить перед текущим символом.
    - `a` — вставить после текущего символа.
    - `I` — вставить в начало строки.
    - `A` — вставить в конец строки.
    - `o` — добавить новую строку ниже.
    - `O` — добавить новую строку выше.
- **Как выйти:** Нажмите **`Esc`** .
---
 3. **Визуальный режим (Visual Mode)**

- Для **выделения текста** перед копированием/удалением.
- **Как активировать:**
    - `v` — выделение символов.
    - `V` — выделение целых строк.
    - `Ctrl + v` — выделение блока (квадрат).
- **Что можно делать:**
    - Выделить текст → `d` (удалить), `y` (копировать), `p` (вставить).
- **Как выйти:** Нажмите **`Esc`** .
---
 4. **Режим замены (Replace Mode)**

- Для замены символов или текста.
- **Как активировать:**
    - `r` — заменить **один символ** под курсором.
    - `R` — заменить **текст** , начиная с текущей позиции (аналог редактирования).
- **Как выйти:** Нажмите **`Esc`** .
---

### 3. **Командный режим (Command-line Mode)**

- Используется для ввода **длинных команд** (например, сохранения или выхода).
- **Как активировать:** Нажмите **`:`** (двоеточие) в нормальном режиме.
- **Примеры команд:**
    - `:w` — сохранить файл.
    - `:q` — выйти из Vim.
    - `:wq` — сохранить и выйти.
    - `:q!` — выйти без сохранения.
- **Важно:** После ввода команды нажмите **Enter** .

---
5. **Режим командной строки (Ex Mode)**

- Режим для выполнения **командных скриптов** (редко используется в повседневной работе).
- **Как активировать:** Нажмите **`Q`** (но чаще его не используют новички).
---
6. **Режим выбора (Select Mode)**

- Похож на визуальный, но позволяет **редактировать выделенный текст** как в графических редакторах.
- **Как активировать:** `gh` (включает режим выбора).
---

