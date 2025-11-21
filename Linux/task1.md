# <span style="color: #ff5733;">РАЗДЕЛ 1 Основы операционных систем</span>
3 Создать отчет о системе

## <span style="color: #4CAF50;">_Задание 1 Установка и настройка системы</span>

### <span style="color: 2196F3;">_1. Установить дистрибутив Ubuntu Desktop.</span>
Ubuntu устанавливается в вирутальную машину развернутую в гипервизоре Proxmox

### <span style="color: 2196F3;">_2. Настроить:</span>

● Сеть (проверить подключение к интернету).
адрес получает автоматически, используя протокол dhcp, от домашнего роутера

● Общие папки с локальной системой (например, через Samba или VirtualBox Shared Folders).
Настройка общей папки используя Samba.
1. Проверить наличие samba
	```bash
	sudo systemctl status smbd
```

1.  Если службы не запущена, запустить ее.
```bash
sudo systemctl start smbd
```

1.  Если SAMBA не установлена, то установить её
```bash
sudo apt update
sudo apt install samba
```

1. Настроить SAMBA
Основной конфигурационный файл Samba находится по пути `/etc/samba/smb.conf`. 
Пример настройки общей папки
Добавить в конец файла следующий блок:
```bash
[shared]
   comment = Shared Folder
   path = /srv/samba/shared
   browseable = yes
   writable = yes
   read only = no
   create mask = 0775
   directory mask = 0775
   valid users = user1, user2
```
- `[shared]` — имя общей папки, которое будет отображаться в сети.
- `comment` — описание папки.
- `path` — путь к папке на сервере.
- `browseable` — разрешает ли папка быть видимой в сети.
- `writable` — разрешает ли запись в папку.
- `read only` — запрещает ли запись (противоположно `writable`).
- `create mask` и `directory mask` — права доступа для создаваемых файлов и директорий.
- `valid users` — список пользователей, которым разрешен доступ, этих пользователей надо добавить в пользователей системы (useradd user1, user2)

1. Создать папку и настроить права
```bash
sudo mkdir -p /srv/samba/shared
sudo chmod -R 775 /srv/samba/shared
sudo chown -R nobody:nogroup /srv/samba/shared
```

1. Добавить пользователей SAMBA
Samba использует собственных пользователей, которые должны быть созданы на основе существующих пользователей Linux.
```bash
sudo smbpasswd -a username
```
Замените `username` на имя пользователя Linux. Вам будет предложено установить пароль для этого пользователя.

1. Перезапустить SAMBA
После настройки перезапустите службу Samba, чтобы применить изменения.
```bash
sudo systemctl restart smbd
```

1. Доступ к общей папке
Теперь вы можете получить доступ к общей папке с другого устройства.
В Windows:
	- Откройте проводник.
	- Введите \\<IP-адрес-сервера>\shared в адресной строке.
	- Введите имя пользователя и пароль, которые вы настроили в Samba.
В Linux:
- Установите клиент Samba:	
```bash
	sudo apt install cifs-utils
```
- Смонтируйте общую папку:
```bash
    sudo mount -t cifs //<IP-адрес-сервера>/shared /mnt -o username=<username>,password=<password>
```


### <span style="color: 2196F3;">_3. Обновить систему и установить доступные обновления.</span>
команды `sudo update` и `sudo upgrade`

### <span style="color: 2196F3;">_4. Сгенерировать SSH-ключ и подключиться к виртуальной машине через SSH с локального ПК.</span>
проверка наличия ssh клиента
```
ssh -V
```
ответ : OpenSSH_8.9p1 Ubuntu-3ubuntu0.3, OpenSSL 3.0.2 15 Mar 2022

установка:
```
sudo apt update
sudo apt install openssh-client
```

проверка наличия ssh сервера
```
ssh localhost
```
если не установлен, то будет сообщение:
ssh: connect to host localhost port 22: Connection refused
установка ssh сервера:
```
sudo apt update
sudo apt install openssh-server
```
проверка работы:
```
sudo systemctl status ssh
```

генерация ключей
```
ssh-keygen -t ed25519
```
на выходе два файла:
<имя_файла> - приватный ключ
<имя_файла>.pub - публичный ключ

копируем публичный ключ на нужный сервер
```bash
ssh-copy-id -i pubkey user@192.168.10.28
```
вывод:
```
/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "my_key.pub"
/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
expr: warning: '^ERROR: ': using '^' as the first character
of a basic regular expression is not portable; it is ignored
/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
stty: standard input: Inappropriate ioctl for device
user@192.168.10.28's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'user@192.168.10.28'"
and check to make sure that only the key(s) you wanted were added.
```

файл конфигурации
```
sudo nano /etc/ssh/sshd_config
```
отключаем вход по паролю и включаем вход по публичному ключу
```
PasswordAuthentication no
PubkeyAuthentication yes
```

После всех настроек перезапускаем ssh-сервер (текущая сессия при этом не обрывается):
```bash
sudo systemctl restart ssh
```


## <span style="color: #4CAF50;">_Задание 2 Изучение характеристик системы</span>

**Требования**
Определить и записать:
●       Версию ядра.
uname -r

Подробная информация о системе (для systemd)
`hostnamectl`
```
user@server:~/deb-packages$ hostnamectl
 Static hostname: server
       Icon name: computer-vm
         Chassis: vm
      Machine ID: b3326a2b125941fab41d9a40f6202937
         Boot ID: 57e2ba4ffdb94c939ba41559365d0c75
  Virtualization: oracle
Operating System: Ubuntu 22.04.5 LTS
          Kernel: Linux 5.15.0-130-generic
    Architecture: x86-64
 Hardware Vendor: innotek GmbH
  Hardware Model: VirtualBox
user@server:~/deb-packages$

```


●       Количество доступной и используемой памяти.
free -h
df -h

●       Список предустановленных пакетов.
dpkg --get-selections



●       Тип загрузчика и его конфигурацию (GRUB, EFI или другой).
Проверка на  EFI:
Проверка наличия каталога `/sys/firmware/efi`
```bash
ls /sys/firmware/efi
```
- Если каталог существует, система использует UEFI.
- Если каталог отсутствует, система использует BIOS (Legacy).
Если система использует UEFI, команда `efibootmgr` покажет информацию о загрузчике.
```bash
sudo efibootmgr
BootCurrent: 0001
Timeout: 1 seconds
BootOrder: 0001,0002,0003
Boot0001* ubuntu
Boot0002* Windows Boot Manager
Boot0003* UEFI: CD/DVD Drive
```

Проверка наличия GRUB
GRUB — это самый распространенный загрузчик в Linux. Проверьте, установлен ли он:
```bash
grub-install --version
grub-install (GRUB) 2.06-2ubuntu7.2
```
Конфигурация GRUB обычно находится в файле `/boot/grub/grub.cfg` (или `/boot/grub2/grub.cfg`)
```bash
cat /boot/grub/grub.cfg
```
Основной конфигурационный файл GRUB находится в `/etc/default/grub`. После изменения этого файла нужно обновить конфигурацию GRUB:
```bash
sudo update-grub
```

Если вы используете systemd-boot (обычно в системах с UEFI), проверьте наличие загрузчика командой bootctl status:
```bash
bootctl status

System:
     Firmware: UEFI 2.70 (American Megatrends 5.13)
  Secure Boot: disabled
   Setup Mode: user
  Current Loader:
      Product: systemd-boot 247.3-1-arch
     Features: ✓ Boot counting
               ✓ Menu timeout control
               ✓ One-shot menu timeout control
               ✓ Default entry control
               ✓ One-shot entry control
```
Конфигурация systemd-boot находится в каталоге `/boot/loader/`

Проверка загрузочного раздела:
Команды:
- sudo parted -l
- lsblk


●       Лог последних загрузок системы.
перечень логов предыдущих загрузок:
```bash
journalctl --list-boots
-8 ce6b98b23d224c4ab7fdac1451935ed0 Thu 2025-01-02 14:56:47 +07—Thu 2025-01-02 15:11:53 +07
-7 68287b19d006464188c5671937b2dc4d Thu 2025-01-02 16:25:43 +07—Thu 2025-01-02 18:47:48 +07
-6 45e5a51ac0d94c0dbd899cb975ad147f Thu 2025-01-02 22:28:03 +07—Thu 2025-01-02 22:48:59 +07
-5 0da5df4cc4b74d568240cc4a7f487e45 Thu 2025-01-02 22:50:19 +07—Fri 2025-01-03 00:36:55 +07
-4 2cff0733450547cb82c0093c78b6c3ac Sat 2025-01-04 19:36:48 +07—Sat 2025-01-04 22:08:11 +07
-3 909567d7427d44f084be9438b397d0ce Sun 2025-01-05 19:39:50 +07—Sun 2025-01-05 19:56:59 +07
-2 d0f93303eee642f2945c0e3cb42396bd Sun 2025-01-05 19:57:07 +07—Sun 2025-01-05 20:09:55 +07
-1 aefe6f80079249fe98b0aeffeb24966a Sun 2025-01-05 20:10:03 +07—Sun 2025-01-05 20:24:18 +07
 0 9fd9eaa11c34470ab2a3157932301725 Mon 2025-01-06 20:42:16 +07—Mon 2025-01-06 21:24:21 +07
```
- Каждая строка соответствует одной загрузке.
- Используйте идентификатор загрузки (например, `-1`) для просмотра логов конкретной загрузки
```bash
journalctl -b -1
```



## <span style="color: #4CAF50;">Задание 3 Отчет о системе</span>

1.     Создать документ, содержащий описание:
●       Основных характеристик системы (архитектура, версия ядра, объем памяти и т.д.).
●       Установленных по умолчанию пакетов.
●       Процесса загрузки системы (с описанием этапов).
 
