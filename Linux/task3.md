# <span style="color: #ff5733;">_РАЗДЕЛ 3 Файловые системы и диски</span>

## <span style="color: #4CAF50;">_Задание 1 Разметка дисков и создание ФС</span>
### <span style="color: 2196F3; ">_1. Создайте 3 виртуальных диска, объемом по 1ГБ.</span>
Диски создаем в GUI прокса конкретной виртуальной мащины

### <span style="color: 2196F3;">_2. Разметьте созданные диски</span>

●       Разметьте disk1.img в формате MBR, создайте один раздел и отформатируйте его в ext4.

для разметки используем parted
1. Сначала нужно определить, какой диск вы хотите разметить. Для этого используйте команду: `lsblk` или `fdisk -l`
2. Создайте таблицу разделов MBR на диске: `sudo parted /dev/sdа mklabel msdos`
3. Создайте раздел:  `sudo parted /dev/sda mkpart primary ext4 1MiB 100%`
4. Отформатируйте раздел в `ext4`: `sudo mkfs.ext4 /dev/sda1`

●       Разметьте disk2.img в формате GPT, создайте два раздела: xfs и btrfs.
1. Создайте таблицу разделов GPT на диске: `sudo parted /dev/sdа mklabel gpt`
2. Создайте первый раздел:  `sudo parted /dev/sda mkpart xfs 1MiB 500MiB`
3. Создайте второй раздел:  `sudo parted /dev/sda mkpart btrfs 500MiB 100%`
4. Отформатируйте первый раздел в `xfs`: `sudo mkfs.xfs /dev/sda1`, если не установлен `apt install xfsprogs`
5. Отформатируйте второй раздел в `btrfs`: `sudo mkfs.btrfs /dev/sda1`, если не установлен `apt install btrfs-progs
`

●       Используйте parted для гибкой разметки третьего диска.

1. **Создание физических томов**:
```
```root@mypc:/home/user# lsblk -e 7
`NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda      8:0    0   50G  0 disk
├─sda1   8:1    0    1M  0 part
├─sda2   8:2    0  513M  0 part /boot/efi
└─sda3   8:3    0 49,5G  0 part /var/snap/firefox/common/host-hunspell
                                /
sdb      8:16   0    5G  0 disk
├─sdb1   8:17   0  2,4G  0 part
└─sdb2   8:18   0  2,6G  0 part
sdc      8:32   0    4G  0 disk
sdd      8:48   0    3G  0 disk
sde      8:64   0    2G  0 disk
sdf      8:80   0    1G  0 disk
└─sdf1   8:81   0 1023M  0 part
sr0     11:0    1    2K  0 rom

root@mypc:/home/user# pvcreate /dev/sdc /dev/sdd
  Physical volume "/dev/sdc" successfully created.
  Physical volume "/dev/sdd" successfully created.

root@mypc:/home/user# pvs
  PV         VG Fmt  Attr PSize PFree
  /dev/sdc      lvm2 ---  4,00g 4,00g
  /dev/sdd      lvm2 ---  3,00g 3,00g

```
**Просмотр информации о физических томах**:
`sudo pvdisplay` или `sudo pvs`
**Удаление физического тома**:
`sudo pvremove /dev/sdX`

1. **Создание группы томов**:
`root@mypc:/home/user# vgcreate my_VG /dev/sdc /dev/sdd`
my_VG имя группы томов
`Volume group "my_VG" successfully created`
**Просмотр информации о группах томов**:
`sudo vgdisplay` или `sudo vgs`
**Расширение группы томов (добавление нового физического тома)**:
`sudo vgextend my_VG /dev/sdX`
**Удаление группы томов**:
`sudo vgremove my_VG`
**Уменьшение группы томов (удаление физического тома)**:
`sudo vgreduce my_VG /dev/sdX`
```
root@mypc:/home/user# vgs
  VG    #PV #LV #SN Attr   VSize VFree
  my_VG   2   0   0 wz--n- 6,99g 6,99g
```

1. **Создание логического тома**:
```
root@mypc:/home/user# lvcreate -l 100%FREE -n my_LV my_VG
  Logical volume "my_LV" created.
```
`-l 100%FREE` использовать все пространство группы томов
`-n my_LV` имя логического тома
`my_VG` группа томов, в которой создается логический том
**Просмотр информации о логических томах**:
`sudo lvdisplay` или `sudo lvs`
**Расширение логического тома**:
`sudo lvextend -L +5G /dev/my_volume_group/my_logical_volume`
	После расширения тома нужно расширить файловую систему (для ext4):
	`sudo resize2fs /dev/my_volume_group/my_logical_volume`
**Уменьшение логического тома**:
Сначала уменьшите файловую систему (для ext4):
`sudo resize2fs /dev/my_volume_group/my_logical_volume 5G`
	Затем уменьшите логический том:
	`sudo lvreduce -L 5G /dev/my_volume_group/my_logical_volume`
**Удаление логического тома**:
`sudo lvremove /dev/my_volume_group/my_logical_volume`

1.  **Команды для работы с файловыми системами**
**Создание файловой системы на логическом томе** (например, ext4):
`sudo mkfs.ext4 /dev/my_volume_group/my_logical_volume`
```
root@mypc:/home/user# mkfs.ext4 /dev/my_VG/my_LV
mke2fs 1.46.5 (30-Dec-2021)
Discarding device blocks: done
Creating filesystem with 1832960 4k blocks and 458752 inodes
Filesystem UUID: b82bab56-b4a5-4e11-b658-5abf1d77181f
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done

```



**Монтирование логического тома**:
`sudo mount /dev/my_volume_group/my_logical_volume /mnt/my_mount_point`
**Автомонтирование при загрузке**:  
Добавь запись в `/etc/fstab`:
`/dev/my_volume_group/my_logical_volume /mnt/my_mount_point ext4 defaults 0 2`


**Проверка целостности LVM**:
`sudo vgscan`
`sudo lvscan`


### <span style="color: 2196F3; ">_3. Настройка файловой системы</span>

●       После создания файловой системы на одном из дисков (например, btrfs), измените настройки inodes, если файловая система это позволяет.

В файловой системе **Btrfs** количество inode не фиксировано, как в некоторых других файловых системах (например, ext4). Btrfs использует динамическую структуру метаданных, и inode создаются по мере необходимости. Это означает, что в Btrfs нет ограничения на количество inode, и нет необходимости беспокоиться о том, что они закончатся.

●       Выполните команды для проверки состояния файловой системы.
**Не проверяйте примонтированные файловые системы.** Это может привести к повреждению данных. Если файловая система примонтирована, сначала отмонтируйте её:


1. **Проверка файловой системы ext2/ext3/ext4**
Проверка файловой системы:
sudo fsck /dev/sdX
Принудительная проверка (даже если файловая система помечена как чистая):
sudo fsck -f /dev/sdX
Автоматическое исправление ошибок:
sudo fsck -y /dev/sdX
Проверка с выводом подробной информации:
sudo fsck -v /dev/sdX

1. **Проверка файловой системы XFS**
Для файловой системы XFS используется утилита `xfs_repair`.
 Проверка файловой системы:
sudo xfs_repair /dev/sdX
Проверка без исправления ошибок (только чтение):
sudo xfs_repair -n /dev/sdX
Принудительная проверка (если файловая система примонтирована):
sudo xfs_repair -f /dev/sdX

2.  **Проверка файловой системы Btrfs**
Для файловой системы Btrfs используется утилита `btrfs check`.
Проверка файловой системы:
sudo btrfs check /dev/sdX
Проверка с исправлением ошибок:
sudo btrfs check --repair /dev/sdX
**Внимание:** Использование `--repair` может быть опасным и должно выполняться только после создания резервной копии данных.
Проверка только метаданных:
sudo btrfs check --check-data-csum /dev/sdX

2. **Проверка файловой системы NTFS (если используется в Linux)**
Для файловой системы NTFS используется утилита `ntfsfix`.
Проверка и исправление ошибок:
sudo ntfsfix /dev/sdX












## <span style="color: #4CAF50;">_Задание 2 Монтирование разделов</span>
### <span style="color: 2196F3; ">_1. Ручное монтирование</span>

●       Смонтируйте созданные разделы в директории /mnt/disk1, /mnt/disk2, /mnt/disk3.
`root@mypc:/home/user# mkdir /mnt/disk1 /mnt/disk2 /mnt/disk3`

```
root@mypc:/home/user# lsblk -e 7
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda             8:0    0   50G  0 disk
├─sda1          8:1    0    1M  0 part
├─sda2          8:2    0  513M  0 part /boot/efi
└─sda3          8:3    0 49,5G  0 part /var/snap/firefox/common/host-hunspell
                                       /
sdb             8:16   0    5G  0 disk
├─sdb1          8:17   0  2,4G  0 part
└─sdb2          8:18   0  2,6G  0 part
sdc             8:32   0    4G  0 disk
└─my_VG-my_LV 252:0    0    7G  0 lvm
sdd             8:48   0    3G  0 disk
└─my_VG-my_LV 252:0    0    7G  0 lvm
sde             8:64   0    2G  0 disk
sdf             8:80   0    1G  0 disk
└─sdf1          8:81   0 1023M  0 part
sr0            11:0    1    2K  0 rom
```

```
root@mypc:/home/user# mount /dev/sdb1 /mnt/disk1
root@mypc:/home/user# mount /dev/sdb2 /mnt/disk2
root@mypc:/home/user# mount /dev/my_VG/my_LV /mnt/disk3

```


●       Проверьте доступность дисков через lsblk и df -h.

```
root@mypc:/home/user# lsblk -e 7
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda             8:0    0   50G  0 disk
├─sda1          8:1    0    1M  0 part
├─sda2          8:2    0  513M  0 part /boot/efi
└─sda3          8:3    0 49,5G  0 part /var/snap/firefox/common/host-hunspell
                                       /
sdb             8:16   0    5G  0 disk
├─sdb1          8:17   0  2,4G  0 part /mnt/disk1
└─sdb2          8:18   0  2,6G  0 part /mnt/disk2
sdc             8:32   0    4G  0 disk
└─my_VG-my_LV 252:0    0    7G  0 lvm  /mnt/disk3
sdd             8:48   0    3G  0 disk
└─my_VG-my_LV 252:0    0    7G  0 lvm  /mnt/disk3
sde             8:64   0    2G  0 disk
sdf             8:80   0    1G  0 disk
└─sdf1          8:81   0 1023M  0 part
sr0            11:0    1    2K  0 rom

root@mypc:/home/user# df -h
Filesystem               Size  Used Avail Use% Mounted on
tmpfs                    795M  3,1M  792M   1% /run
/dev/sda3                 49G   12G   35G  26% /
tmpfs                    3,9G     0  3,9G   0% /dev/shm
tmpfs                    5,0M  4,0K  5,0M   1% /run/lock
/dev/sda2                512M  6,1M  506M   2% /boot/efi
tmpfs                    795M   76K  795M   1% /run/user/128
tmpfs                    795M   60K  795M   1% /run/user/1000
/dev/sdb1                2,5G   50M  2,4G   2% /mnt/disk1
/dev/sdb2                2,6G  5,8M  2,1G   1% /mnt/disk2
/dev/mapper/my_VG-my_LV  6,8G   24K  6,5G   1% /mnt/disk3
```

### <span style="color: 2196F3;">_2. Автоматическое монтирование</span>

●       Добавьте записи для каждого диска в файл /etc/fstab
```
root@mypc:/home/user# blkid
/dev/sdf1: UUID="88491551-f375-4b7a-a232-4a3729ce9a54" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="064287a4-01"
/dev/sdd: UUID="X1aJtl-kb7g-6ugk-uE8o-9S8Q-cyU2-K9035B" TYPE="LVM2_member"
/dev/sdb2: UUID="f8ec36c3-e20a-4918-bb07-22e8bf03ba9b" UUID_SUB="c86c56bb-b98a-4296-b1cc-fdd42e6cea51" BLOCK_SIZE="4096" TYPE="btrfs" PARTLABEL="btrfs" PARTUUID="972ba4e3-3e32-4c6b-9352-9fec7dee15bd"
/dev/sdb1: UUID="a9395011-ccec-4183-b4d4-42cdca291650" BLOCK_SIZE="512" TYPE="xfs" PARTLABEL="xfs" PARTUUID="b112ab29-fccc-470b-983d-15a53db6c117"
/dev/mapper/my_VG-my_LV: UUID="b82bab56-b4a5-4e11-b658-5abf1d77181f" BLOCK_SIZE="4096" TYPE="ext4"
/dev/sdc: UUID="3no2iE-wNDh-XPKA-cAe5-C5C2-dueL-UmCS65" TYPE="LVM2_member"
/dev/sda2: UUID="3A43-13E2" BLOCK_SIZE="512" TYPE="vfat" PARTLABEL="EFI System Partition" PARTUUID="88411c61-488b-44d6-87e9-92f15e50d6d9"
/dev/sda3: UUID="d6e9f729-614f-4df2-affd-9463746be22a" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="acc8e1fb-640a-437c-b25f-5fee84b16de0"
```


```
root@mypc:/home/user# cat /etc/fstab
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda3 during installation
UUID=d6e9f729-614f-4df2-affd-9463746be22a /               ext4    errors=remount-ro 0       1
# /boot/efi was on /dev/sda2 during installation
UUID=3A43-13E2  /boot/efi       vfat    umask=0077      0       1
/swapfile                                 none            swap    sw              0       0


#xfs
UUID=a9395011-ccec-4183-b4d4-42cdca291650 /mnt/disk1 xfs defaults 0 2

#btrfs
UUID=f8ec36c3-e20a-4918-bb07-22e8bf03ba9b /mnt/disk2 btrfs defaults 0 2

#lvm
UUID=b82bab56-b4a5-4e11-b658-5abf1d77181f /mnt/disk3 ext4 defaults 0 2

```

`mount -a`

```
root@mypc:/home/user# ls /mnt/disk1 /mnt/disk2 /mnt/disk3
/mnt/disk1:
test_xfs

/mnt/disk2:
test_btrfs

/mnt/disk3:
lost+found  test_lvm_ext4
```


●       Перезагрузите систему и проверьте, что диски автоматически монтируются.

`reboot now`

```
root@mypc:/home/user# cat /mnt/disk1/test_xfs /mnt/disk2/test_btrfs /mnt/disk3/test_lvm_ext4
xfs
btrfs
lvm ext4
root@mypc:/home/user#
```


## <span style="color: #4CAF50; ">_Задание 3 Мониторинг дисков</span>
### <span style="color: 2196F3;">_1. Просмотр информации о месте</span>

●       Выполните df -h для анализа свободного и занятого пространства.
```
root@mypc:/mnt# df -h
Filesystem               Size  Used Avail Use% Mounted on
tmpfs                    795M  3,1M  792M   1% /run
/dev/sda3                 49G   12G   35G  26% /
tmpfs                    3,9G     0  3,9G   0% /dev/shm
tmpfs                    5,0M  4,0K  5,0M   1% /run/lock
/dev/sda2                512M  6,1M  506M   2% /boot/efi
/dev/sdb2                2,6G  5,8M  2,1G   1% /mnt/disk2
/dev/sdb1                2,5G   51M  2,4G   3% /mnt/disk1
/dev/mapper/my_VG-my_LV  6,8G   28K  6,5G   1% /mnt/disk3
tmpfs                    795M   76K  795M   1% /run/user/128
tmpfs                    795M   60K  795M   1% /run/user/1000
```

●       Создайте несколько крупных файлов (например, 500 МБ, 700 МБ) и найдите их с помощью du:
##### Использование `dd`
Команда `dd` позволяет создать файл определённого размера, заполненный нулями или случайными данными.
	Создание файла размером 100 МБ, заполненного нулями:
		`dd if=/dev/zero of=filename.txt bs=1M count=100`

	Создание файла размером 100 МБ, заполненного случайными данными:
		 dd if=/dev/urandom of=file500mb bs=10M count=50 status=progress

##### Использование `fallocate`
Команда `fallocate` создаёт файл заданного размера, но не заполняет его данными (быстрее, чем `dd`):
`fallocate -l 700M file700mb`

Отсортируйте файлы по размеру:
`du -ah --max-depth=1 | sort -rh`
``` user@mypc  ~  du -ah --max-depth=1 | sort -rh
1,3G    .
701M    ./file700mb
501M    ./file500mb
25M     ./.oh-my-zsh
13M     ./.cache
11M     ./snap
2,0M    ./.zsh-syntax-highlighting
724K    ./.local
156K    ./.config
116K    ./.zcompdump-mypc-5.8.1.zwc
52K     ./.zcompdump-mypc-5.8.1
52K     ./.zcompdump
32K     ./project
16K     ./.ssh
12K     ./.fontconfig
8,0K    ./.zsh_history
8,0K    ./.bash_history
4,0K    ./.zshrc
4,0K    ./.Xauthority
4,0K    ./Videos
4,0K    ./Templates
4,0K    ./Public
4,0K    ./.profile
4,0K    ./Pictures
4,0K    ./Music
4,0K    ./.lesshst
4,0K    ./.histfile
4,0K    ./Downloads
4,0K    ./Documents
4,0K    ./Desktop
4,0K    ./.bashrc
4,0K    ./.bash_logout
0       ./.sudo_as_admin_successful
```


### <span style="color: 2196F3;">_2. Поиск больших файлов</span>

●       Установите и используйте ncdu для анализа использования пространства.
Установка
`sudo apt install ncdu`

Запуск `ncdu`

```ncdu 1.15.1 ~ Use the arrow keys to navigate, press ? for help
--- / ----------------------------------------------------------------------------------------------------------
    6,2 GiB [##########] /snap
    5,0 GiB [########  ] /usr
    4,6 GiB [#######   ] /var
    2,0 GiB [###       ]  swapfile
    1,2 GiB [#         ] /home
  208,7 MiB [          ] /boot
   11,6 MiB [          ] /etc
.   3,2 MiB [          ] /run
  568,0 KiB [          ] /root
  148,0 KiB [          ] /tmp
   76,0 KiB [          ] /mnt
e  16,0 KiB [          ] /lost+found
   12,0 KiB [          ] /srv
e   4,0 KiB [          ] /opt
e   4,0 KiB [          ] /media
e   4,0 KiB [          ] /cdrom
.   0,0   B [          ] /proc
    0,0   B [          ] /sys
    0,0   B [          ] /dev
@   0,0   B [          ]  libx32
@   0,0   B [          ]  lib64
@   0,0   B [          ]  lib32
@   0,0   B [          ]  sbin
@   0,0   B [          ]  lib
@   0,0   B [          ]  bin

```



### <span style="color: 2196F3;">_3. Состояние SMART-дисков</span>

●       Проверьте состояние дисков с помощью smartctl
Установка 
`sudo apt install smartmontools`

Просмотр
```
✘ user@mypc  ~  sudo smartctl -i /dev/sda
smartctl 7.2 2020-12-30 r5155 [x86_64-linux-6.8.0-50-generic] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Vendor:               QEMU
Product:              QEMU HARDDISK
Revision:             2.5+
Compliance:           SPC-3
User Capacity:        53 687 091 200 bytes [53,6 GB]
Logical block size:   512 bytes
LU is thin provisioned, LBPRZ=0
Device type:          disk
Local Time is:        Sat Jan 18 00:19:42 2025 +07
SMART support is:     Unavailable - device lacks SMART capability.
```
диски виртуальные, поэтому не показывает СМАРТ

один из реальных жестких дисков в проксе:
```
root@pve1:~# smartctl -i /dev/sda
smartctl 7.2 2020-12-30 r5155 [x86_64-linux-5.15.30-2-pve] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Model Family:     Western Digital Caviar Green (AF)
Device Model:     WDC WD20EARS-00MVWB0
Serial Number:    WD-WCAZA1409872
LU WWN Device Id: 5 0014ee 2afb4c43e
Firmware Version: 51.0AB51
User Capacity:    2,000,398,934,016 bytes [2.00 TB]
Sector Size:      512 bytes logical/physical
Device is:        In smartctl database [for details use: -P show]
ATA Version is:   ATA8-ACS (minor revision not indicated)
SATA Version is:  SATA 2.6, 3.0 Gb/s
Local Time is:    Sat Jan 18 07:42:51 2025 +07
SMART support is: Available - device has SMART capability.
SMART support is: Enabled
```

```
root@pve1:~# smartctl -A /dev/sda
smartctl 7.2 2020-12-30 r5155 [x86_64-linux-5.15.30-2-pve] (local build)
Copyright (C) 2002-20, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF READ SMART DATA SECTION ===
SMART Attributes Data Structure revision number: 16
Vendor Specific SMART Attributes with Thresholds:
ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  WHEN_FAILED RAW_VALUE
  1 Raw_Read_Error_Rate     0x002f   192   189   051    Pre-fail  Always       -       4189
  3 Spin_Up_Time            0x0027   168   162   021    Pre-fail  Always       -       6591
  4 Start_Stop_Count        0x0032   090   090   000    Old_age   Always       -       10145
  5 Reallocated_Sector_Ct   0x0033   200   200   140    Pre-fail  Always       -       0
  7 Seek_Error_Rate         0x002e   200   200   000    Old_age   Always       -       0
  9 Power_On_Hours          0x0032   090   090   000    Old_age   Always       -       7306
 10 Spin_Retry_Count        0x0032   100   100   000    Old_age   Always       -       0
 11 Calibration_Retry_Count 0x0032   100   100   000    Old_age   Always       -       0
 12 Power_Cycle_Count       0x0032   092   092   000    Old_age   Always       -       8258
192 Power-Off_Retract_Count 0x0032   193   193   000    Old_age   Always       -       5918
193 Load_Cycle_Count        0x0032   186   186   000    Old_age   Always       -       43043
194 Temperature_Celsius     0x0022   119   095   000    Old_age   Always       -       31
196 Reallocated_Event_Count 0x0032   200   200   000    Old_age   Always       -       0
197 Current_Pending_Sector  0x0032   200   200   000    Old_age   Always       -       0
198 Offline_Uncorrectable   0x0030   200   200   000    Old_age   Offline      -       0
199 UDMA_CRC_Error_Count    0x0032   200   200   000    Old_age   Always       -       2
200 Multi_Zone_Error_Rate   0x0008   200   200   000    Old_age   Offline      -       0
```


## <span style="color: #4CAF50;">_Задание 4 Управление дисками</span>

### <span style="color: 2196F3;">_1. Опции монтирования</span>
 
 **Основные опции монтирования**

`defaults`
Использует стандартные параметры монтирования, которые включают:
- `rw`: Чтение и запись.
- `suid`: Разрешение на выполнение SUID и SGID битов.
- `dev`: Интерпретация устройств (файлов блочных устройств) на файловой системе.  
- `exec`: Разрешение на выполнение файлов.   
- `auto`: Автоматическое монтирование при загрузке.   
- `nouser`: Только root может монтировать файловую систему.   
- `async`: Асинхронная запись данных на диск.
`UUID=1234-5678-90AB-CDEF /mnt/data ext4 defaults 0 2`


●       Настройте разные точки монтирования с опциями. 
Только для чтения (ro).
**`ro` и `rw`**
- `ro`: Монтирует файловую систему в режиме только для чтения.   
- `rw`: Монтирует файловую систему в режиме чтения и записи (по умолчанию).
UUID=a9395011-ccec-4183-b4d4-42cdca291650 /mnt/ro xfs ro 0 2


Без кеширования (sync).
**`sync` и `async`**
- `sync`: Синхронная запись данных на диск (медленнее, но безопаснее).    
- `async`: Асинхронная запись данных на диск (быстрее, но менее безопасно).
UUID=a9395011-ccec-4183-b4d4-42cdca291650 /mnt/sync xfs sync 0 2


Ограничение выполнения файлов (noexec).
**`exec` и `noexec`**
- `exec`: Разрешает выполнение файлов на файловой системе (по умолчанию).    
- `noexec`: Запрещает выполнение файлов (полезно для повышения безопасности).
UUID=a9395011-ccec-4183-b4d4-42cdca291650 /mnt/noexec xfs noexec 0 2



### <span style="color: 2196F3;">_2. Настройка квот.</span>


●       Включите поддержку квот на одном из дисков.
 **1. Проверка поддержки квот в ядре**
Убедитесь, что ваше ядро поддерживает квоты. Для этого выполните:
`grep CONFIG_QUOTA /boot/config-$(uname -r)`
Если вы видите `CONFIG_QUOTA=y` или `CONFIG_QUOTA=m`, значит, поддержка квот в ядре включена. Если нет, тебе потребуется перекомпилировать ядро с включенной поддержкой квот.

Установка необходимых пакетов
Если поддержка квот в ядре есть, установи пакеты `quota`:
```
sudo apt update
sudo apt install quota
```

Предположим, необходимо включить квоты на разделе `/dev/sdb1`. Сначала нужно перемонтировать этот раздел с опциями `usrquota` и `grpquota`.
В файле fstab 
Найти строку, соответствующую `/dev/sdb1`, и добавь опции `usrquota` и `grpquota` в список опций монтирования. Например, если строка выглядела так:
```
/dev/sdb1 /mnt/data ext4 defaults 0 2
```
надо изменить ее на:
```
/dev/sdb1 /mnt/data ext4 defaults,usrquota,grpquota 0 2
```

**Перемонтировать раздел:**
```
sudo mount -o remount /mnt/data
```

**Включить квоты**
Включить квоты с помощью команды `quotaon`:
```
sudo quotaon -uvg /mnt/data
```

**Назначить квоты:**
Используй команду `edquota` для редактирования квот. Например, чтобы установить мягкий лимит в 1 ГБ и жесткий лимит в 1.2 ГБ для `user1`, выполни:
```
sudo edquota -u user1
```
Откроется текстовый редактор, в котором нужно изменить значения `soft` и `hard` для блоков.

Аналогично можно установить квоты для групп:
```
sudo edquota -g group1
```


**Проверка состояния квот:**
- **`quota -u [имя_пользователя]`**:
    - Эта команда показывает использование квот для конкретного пользователя. Например: `quota -u user1`.
    - Она выводит информацию о текущем использовании дискового пространства и файлов, а также установленные мягкие и жесткие лимиты.




## <span style="color: #4CAF50;">Задание 5 Тестирование производительности системы</span>

### <span style="color: 2196F3;">_1. Измерение скорости записи</span>

для тестирования можно использовать утилиты:
- **`dd`**: Для быстрого и простого тестирования последовательной скорости чтения/записи.    
- **`fio`**: Для точного и детализированного тестирования с учетом различных параметров (случайный/последовательный доступ, глубина очереди, многопоточность).    
- **`iotop`**: Для мониторинга текущей активности диска в реальном времени.

- открыть отдельно терминал с iotop `sudo iotop -o -d 0.1 -b`, обратить внимание на столбцы `DISK READ` и `DISK WRITE`
- открыть отдельно терминал с dd и запустить запись данных на смонтированный раздел
`sudo dd if=/dev/zero of=./testfile bs=2G count=1 oflag=direct`
- `if=/dev/zero` — источник данных (нули).
- `of=./testfile` — файл для записи.   
- `bs=1G` — размер блока (1 ГБ).    
- `count=1` — количество блоков.    
- `oflag=direct` — запись напрямую на диск, минуя кэш.

●       Для каждой файловой системы выполните тест записи.
 ФС xfs 
 ```
 TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN      IO    COMMAND     
 2119 be/4 user        0.00 B/s    4.11 G/s  ?unavailable?  dd if=/dev/zero of=/mnt/disk1/testfile bs=10MiB count=200 oflag=direct status=progress                                
Total DISK READ:         0.00 B/s | Total DISK WRITE:         4.60 G/s      
Current DISK READ:       0.00 B/s | Current DISK WRITE:       4.60 G/s      

TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN      IO    COMMAND    
2119 be/4 user        0.00 B/s    4.60 G/s  ?unavailable?  dd if=/dev/zero of=/mnt/disk1/testfile bs=10MiB count=200 oflag=direct status=progress      
Total DISK READ:         0.00 B/s | Total DISK WRITE:         4.64 G/s      Current DISK READ:       0.00 B/s | Current DISK WRITE:       4.64 G/s      

TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN      IO    COMMAND        
2119 be/4 user        0.00 B/s    4.64 G/s  ?unavailable?  dd if=/dev/zero of=/mnt/disk1/testfile bs=10MiB count=200 oflag=direct status=progress      Total DISK READ:         0.00 B/s | Total DISK WRITE:         0.00 B/s      Current DISK READ:       0.00 B/s | Current DISK WRITE:    1358.69 M/s
```
ФС btrfs
```
TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN      IO    COMMAND        41 be/4 root       10.83 M/s   13.88 M/s  ?unavailable?  [kworker/u8:1-btrfs-endio-write]                                                          77 be/4 root      130.42 K/s  130.42 K/s  ?unavailable?  [kworker/u8:5-btrfs-endio-write]                                                          271 be/3 root        0.00 B/s  619.49 K/s  ?unavailable?  systemd-journald  
1965 be/4 root        0.00 B/s  391.26 K/s  ?unavailable?  [kworker/u8:0-btrfs-endio-meta]                                                           
Total DISK READ:         0.00 B/s | Total DISK WRITE:         4.31 G/s      
Current DISK READ:       0.00 B/s | Current DISK WRITE:       4.39 G/s      

TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN      IO    COMMAND        41 be/4 root        0.00 B/s  552.85 K/s  ?unavailable?  [kworker/u8:1-events_unbound]                                                             1965 be/4 root        0.00 B/s    4.18 M/s  ?unavailable?  [kworker/u8:0-btrfs-endio-write]                                                          2200 be/4 root        0.00 B/s    4.30 G/s  ?unavailable?  dd if=/dev/zero of=/mnt/disk2/testfile bs=10MiB count=200 oflag=direct status=progress      Total DISK READ:         0.00 B/s | Total DISK WRITE:         4.44 G/s      Current DISK READ:       0.00 B/s | Current DISK WRITE:       4.44 G/s                                                                                  TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN      IO    COMMAND        1965 be/4 root        0.00 B/s    4.64 M/s  ?unavailable?  [kworker/u8:0-btrfs-endio-write]                                                          2200 be/4 root        0.00 B/s    4.44 G/s  ?unavailable?  dd if=/dev/zero of=/mnt/disk2/testfile bs=10MiB count=200 oflag=direct status=progress      Total DISK READ:         0.00 B/s | Total DISK WRITE:         4.27 G/s      Current DISK READ:       0.00 B/s | Current DISK WRITE:       4.27 G/s      

TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN      IO    COMMAND        227 be/3 root        0.00 B/s  314.49 K/s  ?unavailable?  [jbd2/sda3-8]     1965 be/4 root        0.00 B/s    4.64 M/s  ?unavailable?  [kworker/u8:0-btrfs-endio-write]                                                          2200 be/4 root        0.00 B/s    4.27 G/s  ?unavailable?  dd if=/dev/zero of=/mnt/disk2/testfile bs=10MiB count=200 oflag=direct status=progress      Total DISK READ:         0.00 B/s | Total DISK WRITE:      1563.35 K/s      Current DISK READ:       0.00 B/s | Current DISK WRITE:    1215.94 M/s
```

ФС ext4 (lvm)
```
TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN      IO    COMMAND        271 be/3 root        0.00 B/s  245.37 K/s  ?unavailable?  systemd-journald  1633 be/4 user        0.00 B/s   35.05 K/s  ?unavailable?  -zsh             Total DISK READ:         0.00 B/s | Total DISK WRITE:         5.99 G/s      Current DISK READ:       0.00 B/s | Current DISK WRITE:       5.99 G/s                                                                                  TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN      IO    COMMAND        2261 be/4 root        0.00 B/s    5.99 G/s  ?unavailable?  dd if=/dev/zero of=/mnt/disk3/testfile bs=10MiB count=200 oflag=direct status=progress      
Total DISK READ:         0.00 B/s | Total DISK WRITE:         5.99 G/s      Current DISK READ:       0.00 B/s | Current DISK WRITE:       5.99 G/s      

TID  PRIO  USER     DISK READ  DISK WRITE  SWAPIN      IO    COMMAND        2261 be/4 root        0.00 B/s    5.99 G/s  ?unavailable?  dd if=/dev/zero of=/mnt/disk3/testfile bs=10MiB count=200 oflag=direct status=progress      
Total DISK READ:         0.00 B/s | Total DISK WRITE:        34.91 K/s      Current DISK READ:       0.00 B/s | Current DISK WRITE:       2.56 G/s
```


### <span style="color: 2196F3; ">_2. Измерение скорости записи</span>

- Для тестирования скорости чтения, создайте файл и затем прочитайте его:
`sudo dd if=./testfile of=/dev/null bs=1G`

Для тестирования скорости чтения  будем использовать `fio` с параметром read: `fio --name=test --filename=/mnt/disk1/testfile --size=2G --readwrite=read --bs=1M --direct=1`

в портянке выводе читаем строку read
●       Выполните тесты чтения
xfs
`read: IOPS=957, BW=957MiB/s (1004MB/s)(2048MiB/2139msec)`

btrfs
`read: IOPS=947, BW=947MiB/s (993MB/s)(2048MiB/2162msec)`

ext4(lvm)
`read: IOPS=1000, BW=1000MiB/s (1049MB/s)(2048MiB/2047msec)`



### <span style="color: 2196F3;">_3. Тестирование устойчивости к аварийной перезагрузке</span>

●       Создайте файлы на каждом диске.
создал файлы

●       Сделайте аварийную перезагрузку.
 перегрузил принудительно
  
●       Проверьте целостность данных после перезагрузки с помощью утилит.
проверил наличие файлов. 
проверил целостность файловой системы `sudo fsck /dev/sda1`

### <span style="color: 2196F3;">_4. Отчет о нагрузке</span>

Используйте iostat для анализа нагрузки на диски
![[../../../Pasted image 20250119205622.png]]
виртуальные диски находятся на SSD, значения tps низкое.
- tps для **HDD** значения TPS выше 100–200 могут указывать на высокую нагрузку.    
- tps для **SSD** значения TPS в несколько тысяч могут быть нормальными, так как SSD способны обрабатывать гораздо больше операций ввода-вывода в секунду.
одновременно с tps необходимо мониторить параметры await, svctm
**Задержки (await, svctm)**:
- Если TPS высокое, но задержки (await, svctm) низкие, это может быть нормальным.
- Если TPS высокое, а задержки тоже высокие (например, await > 10 мс для SSD или await > 50 мс для HDD), это может указывать на проблемы с производительностью.

Описание параметров:
- **`tps`** (Transfers Per Second):
	- Количество операций ввода-вывода (чтение/запись), выполненных на устройстве за секунду.        
    - Высокое значение `tps` может указывать на высокую нагрузку на диск.
        
- **`kB_read/s`**:    
    - Количество килобайт, прочитанных с устройства за секунду.        
    - Полезно для анализа активности чтения.
        
- **`kB_wrtn/s`**:    
    - Количество килобайт, записанных на устройство за секунду.        
    - Полезно для анализа активности записи.
        
- **`kB_read`**:    
    - Общее количество килобайт, прочитанных с устройства с момента запуска системы.
        
- **`kB_wrtn`**:    
    - Общее количество килобайт, записанных на устройство с момента запуска системы.
    
- **`kB_dscd/s`**:    
    - Это количество данных (в килобайтах в секунду), которые были отброшены (discarded) на устройстве за секунду.        
    - Операция **discard** (или TRIM) сообщает SSD, что определённые блоки данных больше не используются и могут быть физически удалены. Это помогает SSD оптимизировать производительность и продлить срок службы, освобождая место для новых записей.
        
- **`kB_dscd`**:    
    - Это общее количество данных (в килобайтах), которые были отброшены (discarded) на устройстве за всё время мониторинга.


