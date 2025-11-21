# <span style="color: #ff5733;">_РАЗДЕЛ 5 Установка пакетов</span>

## <span style="color: #4CAF50;">_Задание 1 Базовая работа с пакетами</span>

### <span style="color: 2196F3;">_1. Найти определенный пакет по имени или по описанию </span>

Использование `apt`:
`apt search имя_пакета`
`apt list <имя_пакета>`

Просмотрите доступные версии пакета:
`apt list --all-versions <имя_пакета>`

 Поиск по описанию в которых есть слова <ключевое_слово>
`apt search <ключевое_слово>`

Используйте `dpkg -S $(which top)`, чтобы узнать, к какому пакету принадлежит `top`

Просмотр информации о пакете:
`apt show <имя_пакета>`

Использование `dpkg` (для поиска установленных пакетов)
`dpkg -l | grep имя_пакета`

### <span style="color: 2196F3;">_2. Установка/удаление пакетов </span>

●       Установить какой-либо пакет
- Установка пакета:
    `sudo apt install htop`
- Проверка информации о пакете:
	`apt show htop`
- `apt-cache policy htop`
	htop:
	  Installed: (none)
	  Candidate: 3.0.5-7build2
	  Version table:
	     3.0.5-7build2 500
	        500 http://ru.archive.ubuntu.com/ubuntu jammy/main amd64 Packages
	        500 file:/opt/local-repo/deb ./ Packages


●       Обновить установленный пакет
	`sudo apt update`
	`sudo apt install --only-upgrade htop`
	
●       Удалить пакет
- Удаление пакета:    
	`sudo apt remove htop`
- Полное удаление пакета с конфигурационными файлами:
    `sudo apt purge htop`
- Очистка системы от ненужных зависимостей:
    `sudo apt autoremove`

### <span style="color: 2196F3;">_3. Изучить способы отката пакета, в случае его некорректной установки </span>

Рассмотрим на примере:

Моделирование некорректной установки пакета
Установите пакет, например, htop:
`sudo apt-get install htop`
После установки удалите или измените некоторые файлы, связанные с пакетом:
`sudo rm /usr/bin/htop`
Теперь пакет htop будет "сломан", так как его основной исполняемый файл удален.

Откат установки пакета
1. Самый простой способ — переустановить пакет, чтобы восстановить удаленные или измененные файлы.
`sudo apt-get install --reinstall htop`
Это команда переустановит пакет htop, восстановив все файлы до их исходного состояния.

2. Удаление и повторная установка
Если переустановка не помогает, можно полностью удалить пакет, а затем установить его заново.
- Удалите пакет:
`sudo apt-get remove htop`
- Установите пакет заново:
`sudo apt-get install htop`

1. Использование `dpkg` для восстановления
Если пакет был установлен с помощью `dpkg`, можно использовать команду `dpkg --configure` для восстановления.
- Проверьте состояние пакета:
sudo dpkg --configure -a
- Если пакет был частично установлен, эта команда попытается завершить его установку.

1. Использование `apt-get` с опцией `--fix-broken`
Если система сообщает о "сломанных" пакетах, можно попытаться исправить их с помощью:
`sudo apt-get install -f`
Эта команда попытается исправить зависимости и восстановить сломанные пакеты.


Проверка состояния пакета
После выполнения отката можно проверить состояние пакета с помощью:
`dpkg -l htop`
Эта команда покажет статус пакета и его версию.



## <span style="color: #4CAF50;">_Задание 2 Управление зависимостями</span>

### <span style="color: 2196F3;">_1. Установить пакет </span>

Способы:
1. **Использование APT (Advanced Package Tool)**
Основной инструмент для работы с пакетами в Debian.  
`sudo apt install <имя_пакета>`

2. **Установка .deb-файлов вручную**
Если пакет скачан в формате `.deb` (например, с официального сайта):
`sudo dpkg -i <файл.deb>`
**Важно!** Если возникли ошибки зависимостей, выполните:
`sudo apt install -f`  # автоматическое исправление


3. **Snap и Flatpak**
Универсальные пакеты, работающие в изолированных средах.  
**Snap:**
`sudo apt install snapd`
`sudo snap install <имя_пакета>`

**Flatpak:**
`sudo apt install flatpak`
`flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo`
`flatpak install <имя_пакета>`

1. **Установка из исходного кода**
- Скачать исходный код (обычно `.tar.gz`).
- Установите зависимости, указанные в метаданных пакета (например, компиляторы, библиотеки, утилиты)
    `sudo apt build-dep <имя_пакета>`
- Распакуйте архив и выполните:
    `./configure`
    `make`
    `sudo make install`


5. **Добавление сторонних репозиториев**
- Добавьте репозиторий в файл `/etc/apt/sources.list` или в отдельный файл в `/etc/apt/sources.list.d/`.
- Импортируйте GPG-ключ (если требуется):
    `sudo apt-key add <ключ.gpg>`
- Обновите список пакетов:
    `sudo apt update`




### <span style="color: 2196F3;">_2. Проверить какие зависимости требуются для корректной работы пакета </span>


1. **Для пакетов из репозиториев**
a. Через `apt`:
- Показать зависимости пакета (если он доступен в репозиториях):
   `apt show <имя_пакета>`
 В секции **Depends** будут перечислены обязательные зависимости, в **Recommends** — рекомендуемые.
- **Детальный вывод зависимостей**:
    `apt-cache depends <имя_пакета>`
    
b. Для уже установленных пакетов:
Посмотреть зависимости установленного пакета:
    `dpkg -s <имя_пакета> | grep Depends`

2. **Для .deb-файлов**
Если у вас есть локальный `.deb-файл`, проверьте его зависимости:
`dpkg -I <файл.deb> | grep Depends`







### <span style="color: 2196F3;">_3. Попробовать очистить неиспользуемые зависимости </span>

Команда `apt-cache rdepends`:
`apt-cache rdepends <имя_пакета>`
Выведет список пакетов, которые зависят от указанного пакета (включая установленные и не установленные).

Показать **только установленные** зависимости:
`apt-cache rdepends --installed <имя_пакета>`


Удаление неиспользуемых зависимостей, эта команда удаляет пакеты, которые были установлены автоматически как зависимости, но больше не нужны.
`sudo apt autoremove`

Дополнительная очистка (кеш, старые версии пакетов):
`sudo apt clean          # Очистка кеша пакетов`
`sudo apt autoclean      # Удаление старых версий пакетов`

 **Важно!**
- Перед удалением всегда проверяйте список пакетов, которые будут затронуты.
- Некоторые пакеты могут быть помечены как "неиспользуемые" ошибочно — убедитесь, что они не нужны для работы системы.
- Для предотвращения случайного удаления важных пакетов можно добавить их в исключения (зависит от менеджера пакетов). 
Заблокировать версию пакета:
`sudo apt-mark hold <имя_пакета>    # Защита от удаления и обновления`
`sudo apt-mark unhold <имя_пакета>  # Снять блокировку`
Проверить заблокированные пакеты:
`apt-mark showhold`

```
root@mypc:/home/user# apt autoremove
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  libwpe-1.0-1 libwpebackend-fdo-1.0-1
0 upgraded, 0 newly installed, 2 to remove and 0 not upgraded.
After this operation, 182 kB disk space will be freed.
Do you want to continue? [Y/n] n
Abort.

root@mypc:/home/user# apt-cache rdepends --installed libwpe-1.0-1
libwpe-1.0-1
Reverse Depends:
  libwpebackend-fdo-1.0-1
  libwpebackend-fdo-1.0-1
  
root@mypc:/home/user# apt autoremove
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages will be REMOVED:
  libwpe-1.0-1 libwpebackend-fdo-1.0-1
0 upgraded, 0 newly installed, 2 to remove and 0 not upgraded.
After this operation, 182 kB disk space will be freed.
Do you want to continue? [Y/n] Y
(Reading database ... 188574 files and directories currently installed.)
Removing libwpebackend-fdo-1.0-1:amd64 (1.14.2-0ubuntu0.22.04.1) ...
Removing libwpe-1.0-1:amd64 (1.14.0-0ubuntu0.22.04.1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.8) ...
root@mypc:/home/user#

```



## <span style="color: #4CAF50;">_Задание 3 Развёртывание LAMP/LEMP стека</span>
### <span style="color: 2196F3;">_1. Установить стек LAMP (Apache, MySQL/MariaDB, PHP) или LEMP (Nginx, MySQL/MariaDB, PHP). </span>

**LAMP Stack (Apache)**
	1. **Обновите пакеты:**
	    `sudo apt update && sudo apt upgrade -y`
	2.  **Установка Apache:**
	    `sudo apt install apache2 -y`
	    `sudo systemctl enable --now apache2`
	    Проверьте: откройте в браузере `http://ваш-ip-сервера`.
	    РАБОТАЕТ
	3.  **Установка MariaDB (MySQL):**
	    `sudo apt install mariadb-server mariadb-client -y`
	    `sudo mysql_secure_installation`
	    Ответьте на вопросы безопасности в мастере настройки.
	4.  **Установка PHP:**
	    `sudo apt install php libapache2-mod-php php-mysql php-cli php-curl php-zip php-mbstring -y`
	    `sudo systemctl restart apache2`
	5.  **Проверка PHP:**
	    `echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php`
	    Откройте `http://ваш-ip-сервера/info.php`.
	    РАБОТАЕТ, в браузере "# PHP Version 8.1.2-1ubuntu2.20"

Удаление стека LAMP
```
sudo systemctl stop apache2 mariadb
sudo apt purge apache2 mariadb-server mariadb-client php*
sudo apt autoremove -y
sudo rm -rf /etc/apache2 /var/www/html /var/lib/mysql /etc/mysql /etc/php
```

Чтобы **полностью удалить LAMP**, выполните:
Для Apache:
`sudo apt purge apache2* apache2-utils apache2-bin apache2-data -y`
Для MariaDB:
`sudo apt purge mariadb-* libmariadb* -y`
Для остатков PHP (если есть):
`sudo apt purge php* -y`
Очистите зависимости:
`sudo apt autoremove -y`
Удалите конфиги (опционально):
`sudo rm -rf /etc/apache2 /var/www/html /var/lib/mysql /etc/mysql`






**LEMP Stack (Nginx)**
	1. **Обновление пакетов** (аналогично LAMP).
	2. **Установка Nginx:**
	    `sudo apt install nginx -y`
	    `sudo systemctl enable --now nginx`
	    Проверьте: `http://ваш-ip-сервера`.
	    РАБОТАЕТ, в браузере:
```
# Welcome to nginx!

If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to [nginx.org](http://nginx.org/).  
Commercial support is available at [nginx.com](http://nginx.com/).

_Thank you for using nginx._
```



	3. **Установка MariaDB** (аналогично LAMP).
	4. **Установка PHP-FPM:**
	    `sudo apt install php-fpm php-mysql php-cli php-curl php-zip php-mbstring -y`
	5. **Настройка Nginx для PHP:**
	    - Отредактируйте конфиг сайта по умолчанию:
	        `sudo nano /etc/nginx/sites-available/default`
	    - В блоке `server` добавьте:
	        ```
	           location ~ \.php$ {
	            include snippets/fastcgi-php.conf;
	            fastcgi_pass unix:/run/php/php-fpm.sock;
	        }```
	    - Проверьте конфигурацию и перезапустите Nginx:
	        `sudo nginx -t`
	        `sudo systemctl restart nginx`
	6. **Проверка PHP** (аналогично LAMP).
		РАБОТАЕТ, в браузере "# PHP Version 8.1.2-1ubuntu2.20"








### <span style="color: 2196F3;">_2. Настроить phpMyAdmin для работы с базами данных. </span>

ставим на пустую ubuntu server 24.10
поставить nano

1. Установка Nginx
sudo apt install nginx -y
sudo systemctl enable nginx --now

Проверьте доступность Nginx:
curl -I 127.0.0.1

 2. Установка PHP и расширений
sudo apt install php-fpm php-mysql php-mbstring php-xml php-zip php-curl -y

Проверьте версию PHP:
php -v

3. Установка MySQL
sudo apt install mysql-server -y
sudo mysql_secure_installation
проверим работу
`sudo systemctl status mysql

4. Установка phpMyAdmin
sudo apt install phpmyadmin -y
- При выборе веб-сервера нажмите TAB → ПРОПУСТИТЕ выбор (так как используем Nginx)
- Подтвердите настройку базы данных для phpMyAdmin

Настроить пользователя root БД mysql, назначить ему пароль.
1.Войдите в MySQL как `root`:
```
sudo mysql -u root
```
2.При необходимости измените плагин аутентификации и установите нужный пароль:
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'ваш_новый_пароль';
```
3.Примените изменения:
```
FLUSH PRIVILEGES;
```

3. Настройка Nginx
Создайте конфиг:
sudo nano /etc/nginx/sites-available/phpmyadmin.conf
Добавьте содержимое:
```nginx
server {
    listen 80;
    server_name your_server_ip_or_domain;

    root /usr/share/phpmyadmin;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Активируйте конфиг:
sudo ln -s /etc/nginx/sites-available/phpmyadmin.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

5.  Назначьте права доступа
sudo chown -R www-data:www-data /usr/share/phpmyadmin

6.  Проверка работы
Откройте в браузере:
http://your_server_ip/phpmyadmin
- Используйте логин/пароль MySQL (root или созданного пользователя)

### <span style="color: 2196F3;">_3. Найти и запустить простой проект с GitHub. </span>

Рассмотрим подробно шаги разворачивания в ОС Ubuntu server 24.10 проекта BookStack https://github.com/BookStackApp/BookStack.git с веб сервером nginx и БД mysql.

Установка зависимостей
Nginx:
sudo apt install nginx -y
sudo systemctl enable nginx --now

PHP 8.3 + расширения:
sudo apt install php-fpm php-common php-mysql php-curl php-zip php-mbstring php-xml php-gd php-tidy php-bcmath -y

MySQL:
sudo apt install mysql-server -y
sudo systemctl enable mysql --now
sudo mysql_secure_installation  # Ответьте на вопросы безопасности


Настройка базы данных
1. Войдите в MySQL:
   sudo mysql -u root -p

2. Создайте БД и пользователя:
```sql
CREATE DATABASE bookstack;
CREATE USER 'bookstack'@'localhost' IDENTIFIED BY 'your_strong_password_here';
GRANT ALL PRIVILEGES ON bookstack.* TO 'bookstack'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

Установка Composer
`curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer

Установка BookStack
1. Клонируйте репозиторий:
```
sudo git clone https://github.com/BookStackApp/BookStack.git /var/www/bookstack
cd /var/www/bookstack
sudo git checkout release  # Используйте стабильную версию
```

2. Установите зависимости:
`sudo composer install --no-dev

3. Настройте окружение:
```bash
sudo cp .env.example .env
sudo nano .env
```

**Основные настройки в `.env`:**
```ini
APP_URL=http://your-domain.com  # Или IP-адрес сервера
DB_HOST=127.0.0.1
DB_DATABASE=bookstack
DB_USERNAME=bookstack
DB_PASSWORD=your_strong_password_here
```

4. Сгенерируйте ключ:
`sudo php artisan key:generate

5. Установите права:
```bash
sudo chown -R www-data:www-data /var/www/bookstack
sudo chmod -R 755 /var/www/bookstack/storage
sudo chmod -R 755 /var/www/bookstack/bootstrap/cache
```

Настройка Nginx

1. Создайте конфиг:
`sudo nano /etc/nginx/sites-available/bookstack.conf

2. Вставьте конфигурацию:
  
```nginx
server {
    listen 80;
    server_name 192.168.10.2;  # Только IP без пути!

    root /var/www/bookstack/public;
    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;  
        include fastcgi_params;
    }

    # Запрет доступа к скрытым файлам
    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

3. Активируйте конфиг:
```bash
sudo ln -s /etc/nginx/sites-available/bookstack.conf /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx
```


Завершение установки

1. Запустите миграции БД:
	sudo php artisan migrate
2. Перейдите в браузере по адресу `http://your-domain.com`
3. Создайте администратора:
    - Email: `admin@admin.com`
    - Пароль: `password` (смените сразу после входа!)



### <span style="color: 2196F3;">_4. Написать пошаговую инструкцию по установке. </span>
смотри выше пункт 3.

## <span style="color: #4CAF50;">_Задание 4 Создание пакета</span>

### <span style="color: 2196F3;">_1. Собрать пакет .deb или .rpm с конфигурационными файлами. </span>

Вот пример создания пакета `.deb` с конфигурационными файлами на примере гипотетического сервиса `mydaemon`.

1. Создание структуры каталогов
```
mkdir -p mydaemon-package/DEBIAN
mkdir -p mydaemon-package/etc/mydaemon
mkdir -p mydaemon-package/usr/bin
```

 1. Добавление конфигурационного файла
Создайте файл `mydaemon.conf` в директории `mydaemon-package/etc/mydaemon/`:
`echo "LOG_LEVEL=info" > mydaemon-package/etc/mydaemon/mydaemon.conf`

 2. Добавление исполняемого файла (пример)
Создайте простой скрипт в `mydaemon-package/usr/bin/mydaemon`:
```
echo '#!/bin/bash
echo "MyDaemon started with config: $(cat /etc/mydaemon/mydaemon.conf)"' > mydaemon-package/usr/bin/mydaemon
chmod +x mydaemon-package/usr/bin/mydaemon
```

 1. Создание control-файла
Создайте `mydaemon-package/DEBIAN/control`:
```
Package: mydaemon
Version: 1.0
Section: base
Priority: optional
Architecture: all
Maintainer: Your Name <you@example.com>
Description: Пример сервиса с конфигурацией
  MyDaemon - демонстрационный сервис для примера пакета .deb
```


 1. Указание конфигурационных файлов (опционально)
Для явного указания конфигурационных файлов создайте `mydaemon-package/DEBIAN/conffiles`, который явно указывает, какие файлы в вашем пакете являются **конфигурационными**:
`/etc/mydaemon/mydaemon.conf`

 2. Скрипт обработки конфигов (опционально)
Создайте скрипт пост-установки `mydaemon-package/DEBIAN/postinst`:
```
#!/bin/bash

#Проверка существования конфига
if [ ! -f /etc/mydaemon/mydaemon.conf ]; then
    cp /etc/mydaemon/mydaemon.conf.dpkg-new /etc/mydaemon/mydaemon.conf
fi
```

Сделайте скрипт исполняемым:
`chmod 755 mydaemon-package/DEBIAN/postinst`

 1. Сборка пакета
`dpkg-deb --build mydaemon-package`
```
user@server:~$ dpkg-deb --build mydaemon-package/
dpkg-deb: building package 'mydaemon' in 'mydaemon-package.deb'.
```
Результат: файл `mydaemon-package.deb`

 1. Проверка пакета
Установите пакет:
`sudo apt install ./mydaemon-package.deb`

при появлении ошибки 
```
N: Download is performed unsandboxed as root as file '/home/user/mydaemon-package.deb' couldn't be accessed by user '_apt'. - pkgAcquire::Run (13: Permission denied)
```
Ошибка возникает из-за проблем с правами доступа к файлу `.deb` для пользователя `_apt`, который используется в Ubuntu/Debian для безопасной работы с пакетами. 
Для исправления сделайте следующее: 
`chmod o+x mydaemon-package.deb
`-rw-r--r-x 1 user user  958 Jan 25 08:15 mydaemon-package.deb`
«Добавить право на выполнение для всех пользователей, кроме владельца и группы»_.


Проверьте расположение файлов:
`ls /etc/mydaemon      # Должен показать mydaemon.conf`
`ls /usr/bin/mydaemon  # Должен показать исполняемый файл`

 Особенности конфигурационных файлов:
1. Файлы в `/etc` автоматически считаются конфигурационными
2. При обновлении пакета старые версии конфигов сохраняются с расширением `.dpkg-old`
3. Для сложных сценариев используйте `debconf` и `maintscripts`

Для проверки качества пакета можно использовать:
`lintian mydaemon-package.deb`

```
user@server:~$ lintian mydaemon-package.deb
E: mydaemon: no-changelog usr/share/doc/mydaemon/changelog.gz (native package)
E: mydaemon: no-copyright-file
E: mydaemon: wrong-file-owner-uid-or-gid etc/ 1000/1000
E: mydaemon: wrong-file-owner-uid-or-gid etc/mydaemon/ 1000/1000
E: mydaemon: wrong-file-owner-uid-or-gid etc/mydaemon/mydaemon.conf 1000/1000
E: mydaemon: wrong-file-owner-uid-or-gid ... use --no-tag-display-limit to see all (or pipe to a file/p
W: mydaemon: description-starts-with-leading-spaces line 1
W: mydaemon: maintainer-script-ignores-errors [postinst]
W: mydaemon: no-manual-page usr/bin/mydaemon
W: mydaemon: non-standard-dir-perm etc/ 0775 != 0755
W: mydaemon: non-standard-dir-perm etc/mydaemon/ 0775 != 0755
W: mydaemon: non-standard-dir-perm usr/ 0775 != 0755
W: mydaemon: non-standard-dir-perm ... use --no-tag-display-limit to see all (or pipe to a file/program
W: mydaemon: non-standard-executable-perm usr/bin/mydaemon 0775 != 0755
W: mydaemon: non-standard-file-perm etc/mydaemon/mydaemon.conf 0664 != 0644
W: mydaemon: unknown-section base
```


### <span style="color: 2196F3;">_2. Ознакомиться с процессом упаковки файлов в пакет. </span>
Ознакомился, см выше :-)

## <span style="color: #4CAF50;">_Задание 5 Настройка локального репозитория</span>

### <span style="color: 2196F3;">_1. Создать локальный репозиторий для пакетов .deb или .rpm. </span>

#### Шаг 1: Подготовка системы
Установите инструменты для работы с репозиторием:
`sudo apt update`
`sudo apt install -y dpkg-dev apt-utils wget`

**Пакет dpkg-dev**
Это набор инструментов для работы с пакетами `.deb` на низком уровне. Он включает утилиты для сборки, распаковки и анализа пакетов.
- **Зачем нужен?**  
    Для создания локального репозитория нам понадобится утилита `dpkg-scanpackages`, которая входит в этот пакет. Она сканирует каталог с `.deb`-пакетами и генерирует файл метаданных `Packages`, необходимый для работы репозитория.

**Пакет apt-utils**
Дополнительные утилиты для работы с APT (Advanced Package Tool).
- **Зачем нужен?**  
    Включает инструменты вроде `apt-extracttemplates`, которые помогают извлекать шаблоны конфигурации из пакетов. Хотя для базового создания репозитория эта утилита не критична, она часто используется в сценариях автоматизации.
- **Почему ставим?**  
    Чтобы обеспечить полную совместимость и доступ к вспомогательным инструментам APT.


#### Шаг 2: Скачивание пакетов
Скачаем пакеты `htop` и `wget` в директорию ~/deb-packages:
`mkdir -p ~/deb-packages`
`cd ~/deb-packages`
Скачиваем пакеты (пример для Ubuntu 22.04 Jammy)
`wget wget http://archive.ubuntu.com/ubuntu/pool/main/h/htop/htop_3.0.5-7build2_amd64.deb`
`wget http://archive.ubuntu.com/ubuntu/pool/main/w/wget/wget_1.21.2-2ubuntu1_amd64.deb`

в случае необходимости 
определяем доступную версию пакета 
`apt-cache policy htop`
и на сайте https://pkgs.org определяем адрес по которому качаем пакет бинарник



#### Шаг 3: Создание структуры репозитория
Создадим каталог для репозитория `/opt/local-repo/deb` и скопируем туда наши пакеты:
`sudo mkdir -p /opt/local-repo/deb`
`sudo cp ~/deb-packages/*.deb /opt/local-repo/deb/`

#### Шаг 4: Генерация метаданных
Перейдите в каталог репозитория и сгенерируйте файл `Packages.gz`:
`cd /opt/local-repo/deb`
`sudo dpkg-scanpackages . /dev/null | sudo gzip -9c | sudo tee Packages.gz > /dev/null`

```
user@server:/opt/local-repo/deb$ sudo dpkg-scanpackages . /dev/null | sudo gzip -9c | sudo tee Packages.gz > /dev/null
dpkg-scanpackages: warning: Packages in archive but missing from override file:
dpkg-scanpackages: warning:   htop wget
dpkg-scanpackages: info: Wrote 2 entries to output Packages file.
user@server:/opt/local-repo/deb$

```

**dpkg-scanpackages**
- **Что это?**  
    Утилита, которая анализирует `.deb`-пакеты в указанном каталоге и создает файл `Packages` с информацией о пакетах (название, версия, зависимости и т.д.).
- **Как работает?**
    - Сканирует текущий каталог (`.`) на наличие `.deb`-файлов.
    - Второй аргумент (`/dev/null`) указывает на файл с информацией о уже установленных пакетах (в данном случае игнорируется).
    - Результат передается в `gzip` для сжатия в `Packages.gz`.
    - `sudo tee` — запускает `tee` с правами root, чтобы записать данные в файл `Packages.gz`, даже если директория защищена.
    - `> /dev/null` — подавляет вывод `tee` в терминал (иначе вы увидите бинарные данные сжатого файла).
- **Ключевые опции**:
    - `-m`: игнорировать ошибки в пакетах.
    - `-a ARCH`: указать архитектуру пакетов (например, `amd64`, `arm64`).
- **Пример**:
	`dpkg-scanpackages -m . /dev/null > Packages  # Без сжатия`

---
 **Зачем это нужно?**
- **Без `dpkg-dev`** вы не сможете сгенерировать метаданные репозитория, и APT не увидит ваши пакеты.
- **Без `dpkg-scanpackages`** файл `Packages.gz` не будет создан, и репозиторий останется «пустым» для клиентов.
---
**Пример полной команды генерации**
`cd /opt/local-repo/deb`
`dpkg-scanpackages . /dev/null | gzip -9c > Packages.gz`
- `gzip -9c`: максимальное сжатие (`-9`), вывод в стандартный поток (`-c`).
- Результат: файл `Packages.gz`, который APT использует для поиска пакетов.

**Важные нюансы**
1. **Обновление репозитория**  
    После добавления новых пакетов в каталог всегда перезапускайте `dpkg-scanpackages`, чтобы обновить `Packages.gz`.
2. **Подпись репозитория**  
    Если хотите избежать использования `[trusted=yes]`, подпишите репозиторий GPG-ключом. Для этого понадобятся дополнительные утилиты:
    `sudo apt install -y gnupg`
3. **Структура каталогов**  
    Для поддержки нескольких архитектур (например, `amd64`, `i386`) создайте подкаталоги:
    /opt/local-repo/deb
    ├── amd64
    ├── i386
    └── all
    Затем используйте `dpkg-scanpackages` с указанием архитектуры:
    `dpkg-scanpackages -a amd64 amd64 /dev/null | gzip -9c > amd64/Packages.gz`


При сканировании пакетов в команду рекомендуется добавлять флаг **`-c <компонент>`**
пример `dpkg-scanpackages -c local . /dev/null | gzip -9c > Packages.gz`
Почему это важно?
- **Структура репозитория Ubuntu**:  
    Стандартные репозитории имеют компоненты (`main`, `universe` и др.), которые помогают APT управлять приоритетами и зависимостями. Исключаем метку `unknown`.
- **Локальный репозиторий**:  
    Если не указать компонент, APT не сможет корректно классифицировать пакеты, что иногда приводит к конфликтам или непредсказуемому поведению.






### <span style="color: 2196F3;">_2. Настроить автоматическое обновление через репозиторий. </span>
#### Шаг 5: Настройка клиента
Добавьте репозиторий в систему. Отредактируйте файл `/etc/apt/sources.list`:
`echo "deb [trusted=yes] file:/opt/local-repo/deb ./" | sudo tee -a /etc/apt/sources.list`

Обновите кеш пакетов:
`sudo apt update`

#### Шаг 6: Проверка работы
Убедитесь, что пакеты видны в системе:
`apt search htop`
Вывод должен включать строку:
`htop/unknown 3.2.1-2 amd64`
Попробуйте установить пакет:
`sudo apt install htop`

```
user@server:/opt/local-repo/deb$ apt-cache policy htop
	htop:
	  Installed: (none)
	  Candidate: 3.0.5-7build2
	  Version table:
	     3.0.5-7build2 500
	        500 http://ru.archive.ubuntu.com/ubuntu jammy/main amd64 Packages
	        500 file:/opt/local-repo/deb ./ Packages
```

явно указав на локальный репозиторий
`sudo apt install htop --target-release "a=file:/opt/local-repo/deb"`

переустановка пакета 
`sudo apt install --reinstall htop -o "APT::Get::Always-Use-Source-Identifier=file:/opt/local-repo/deb"`

#### Как обновлять репозиторий
Добавьте новые `.deb`-пакеты в `/opt/local-repo/deb`.
Перегенерируйте метаданные, после добавления новых пакетов в каталог всегда перезапускайте `dpkg-scanpackages`, чтобы обновить `Packages.gz`
`cd /opt/local-repo/deb`
`sudo dpkg-scanpackages . /dev/null | sudo gzip -9c > Packages.gz`
На клиентах выполните:
`sudo apt update`

#### Для доступа к репозиторию по сети используйте веб-сервер
Если нужно сделать репозиторий доступным для других машин в сети:
1. Установите nginx:
	`sudo apt install -y nginx`
2. Настройте nginx:
	Создайте конфигурацию для репозитория:
	```
	sudo tee /etc/nginx/sites-available/local-repo << EOF
	server {
	    listen 80;
	    server_name _;
	    root /opt/local-repo/deb;
	    autoindex on;
	    location / {
	        try_files \$uri \$uri/ =404;
	    }
	}
	EOF
	```
3. Активируйте конфиг и перезапустите nginx:
	`sudo ln -s /etc/nginx/sites-available/local-repo /etc/nginx/sites-enabled/`
	`sudo systemctl restart nginx`
4. На клиентских машинах добавьте репозиторий:
	`echo "deb [trusted=yes] http://IP_СЕРВЕРА/deb ./" | sudo tee -a /etc/apt/sources.list`
	`sudo apt update`







## <span style="color: #4CAF50;">_Задание 6 Написание автоматизированного скрипта установки</span>

### <span style="color: 2196F3;">Написать универсальный скрипт, который: </span>

●       Определяет текущую ОС и выбирает соответствующий пакетный менеджер.
●       Устанавливает необходимые пакеты и зависимости.
●       Настраивает базовые конфигурации для сервисов.
●       Создаёт тестовую базу данных.
●       Настраивает фаервол.


```bash

#!/usr/bin/env bash
set -euo pipefail

# Проверка прав суперпользователя
if [[ $EUID -ne 0 ]]; then
    echo "Этот скрипт должен быть запущен с правами root" 
    exit 1
fi

# Определение ОС и пакетного менеджера
detect_os() {
    if [ -f /etc/os-release ]; then
        . /etc/os-release
        OS=$ID
    else
        echo "Не удалось определить ОС"
        exit 1
    fi

    case $OS in
        debian|ubuntu) 
            PKG_MANAGER="apt-get"
            PG_SERVICE="postgresql"
            ;;
        centos|rhel|ol) 
            PKG_MANAGER="yum"
            PG_SERVICE="postgresql-server"
            ;;
        fedora)
            PKG_MANAGER="dnf"
            PG_SERVICE="postgresql-server"
            ;;
        arch)
            PKG_MANAGER="pacman"
            PG_SERVICE="postgresql"
            ;;
        *)
            echo "Неподдерживаемая ОС: $OS"
            exit 1
            ;;
    esac
}

# Установка пакетов
install_packages() {
    $PKG_MANAGER update -y
    
    local packages=(
        "postgresql"
        "postgresql-contrib"
        "nginx"
        "python3"
        "curl"
        "wget"
    )

    # Установка пакетов в зависимости от ОС
    case $OS in
        debian|ubuntu)
            $PKG_MANAGER install -y "${packages[@]}"
            ;;
        centos|rhel|ol|fedora)
            $PKG_MANAGER install -y "${packages[@]/postgresql-contrib/postgresql-contrib}"
            ;;
        arch)
            pacman -Sy --noconfirm "${packages[@]}"
            ;;
    esac
}

# Настройка PostgreSQL
setup_postgres() {
    # Инициализация БД (для RHEL-based систем)
    if [[ $OS == "centos" || $OS == "rhel" || $OS == "ol" || $OS == "fedora" ]]; then
        postgresql-setup --initdb
    fi

    systemctl enable --now $PG_SERVICE

    # Создание тестовой БД и пользователя
    sudo -u postgres psql <<EOF
CREATE DATABASE testdb;
CREATE USER testuser WITH PASSWORD 'testpass';
GRANT ALL PRIVILEGES ON DATABASE testdb TO testuser;
EOF

    # Настройка аутентификации
    local pg_hba="/etc/postgresql/*/main/pg_hba.conf"
    [[ $OS == "centos" ]] && pg_hba="/var/lib/pgsql/data/pg_hba.conf"
    
    echo "host testdb testuser 0.0.0.0/0 md5" >> $pg_hba

    # Перезагрузка PostgreSQL
    systemctl restart $PG_SERVICE
}

# Настройка фаервола
setup_firewall() {
    case $OS in
        debian|ubuntu|arch)
            if ! command -v ufw &> /dev/null; then
                $PKG_MANAGER install -y ufw
            fi
            ufw allow OpenSSH
            ufw allow 'Nginx Full'
            ufw allow 5432/tcp  # PostgreSQL
            ufw --force enable
            ;;
        centos|rhel|ol|fedora)
            if ! command -v firewall-cmd &> /dev/null; then
                $PKG_MANAGER install -y firewalld
            fi
            systemctl start firewalld
            firewall-cmd --permanent --add-service=ssh
            firewall-cmd --permanent --add-service=http
            firewall-cmd --permanent --add-service=https
            firewall-cmd --permanent --add-port=5432/tcp
            firewall-cmd --reload
            ;;
    esac
}

# Настройка Nginx
setup_nginx() {
    cat > /etc/nginx/sites-available/default <<EOF
server {
    listen 80 default_server;
    root /var/www/html;
    index index.html;
    
    location / {
        try_files \$uri \$uri/ =404;
    }
}
EOF

    systemctl enable --now nginx
}

# Основная функция
main() {
    detect_os
    install_packages
    setup_postgres
    setup_firewall
    setup_nginx
    
    echo "Настройка завершена!"
    echo "Данные тестовой БД:"
    echo "DB: testdb"
    echo "User: testuser"
    echo "Password: testpass"
    echo "Port: 5432"
}

main

```


