# UrTISI

1) создаем виртуальную машину,linux
2) загружаемся с диска archlinux
3) выбираем boot Arch Linux
4) проверяем с помощью lsblk наш созданный диск и что он видится в ОС
5) с помощью fdisk или cfdisk делим диск на несколько частей 
				1gb - boot
				4.5gb - /
				4.5gb - /var\home
                                                         Disk: /dev/sda
                                         Size: 10 GiB, 10737418240 bytes, 20971520 sectors
                                   Label: gpt, identifier: 7E459494-6D79-E348-A86B-A356E487131B

    Device                              Start                 End            Sectors             Size Type
>>  /dev/sda1                            2048             2099199            2097152               1G EFI System                   
    /dev/sda2                         2099200            11536383            9437184             4.5G Linux root (x86-64)
    /dev/sda3                        11536384            20971486            9435103             4.5G Linux filesystem
6) с помощью lsblk проверяем опять что у нас создались разделы
root@archiso ~ # lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0    7:0    0 508.2M  1 loop /run/archiso/sfs/airootfs
sda      8:0    0    10G  0 disk 
├─sda1   8:1    0     1G  0 part 
├─sda2   8:2    0   4.5G  0 part 
└─sda3   8:3    0   4.5G  0 part 
sr0     11:0    1   622M  0 rom  /run/archiso/bootmnt
7) начинаем создавать фс с помощью mkfs.* на наших партициях
						fat32 /boot
						ext4\xfs /
            root@archiso ~ # mkfs.fat -F32 /dev/sda1
mkfs.fat 4.1 (2017-01-24)
root@archiso ~ # mkfs.ext4 /dev/sda2 
mke2fs 1.45.3 (14-Jul-2019)
/dev/sda2 contains a ext4 file system
        last mounted on /mnt on Mon Sep 23 14:24:32 2019
Proceed anyway? (y,N) y
Creating filesystem with 1179648 4k blocks and 294912 inodes
Filesystem UUID: 20672a30-9811-44a8-b661-a6c7c4e73dcc
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done 

root@archiso ~ # mkfs.ext4 /dev/sda3
mke2fs 1.45.3 (14-Jul-2019)
/dev/sda3 contains a ext4 file system
        last mounted on Mon Sep 23 14:25:48 2019
Proceed anyway? (y,N) y
Creating filesystem with 1179387 4k blocks and 294912 inodes
Filesystem UUID: bb8e8871-d9fc-480a-8d15-601717ffc691
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done 
8) после создания фс монтируем с помощью mount все партиции в директорию /mnt
root@archiso ~ # mount /dev/sda2 /mnt 
root@archiso ~ # mkdir -p /mnt/boot/efi | mount /dev/sda1 /mnt/boot/efi
root@archiso ~ # mkdir -p /mnt/home | mount /dev/sda3 /mnt/home
root@archiso ~ # lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0    7:0    0 508.2M  1 loop /run/archiso/sfs/airootfs
sda      8:0    0    10G  0 disk 
├─sda1   8:1    0     1G  0 part /mnt/boot/efi
├─sda2   8:2    0   4.5G  0 part /mnt
└─sda3   8:3    0   4.5G  0 part /mnt/home
sr0     11:0    1   622M  0 rom  /run/archiso/bootmnt
9) проверяем зеркало с пакетами для арч и ставим яндексовский, т.к. нам до него ближе соответственно скорость закаки быстрее
1 root@archiso ~ # cat /etc/pacman.d/mirrorlist | grep yandex > /etc/pacman.d/mirrorlist                                        :(
root@archiso ~ # cat /etc/pacman.d/mirrorlist                                         
Server = http://mirror.yandex.ru/archlinux/$repo/os/$arch
10) с помощью pacstrap начинаем заливать устанавливать базовые пакеты нашей OS
root@archiso ~ # pacstrap /mnt base base-devel
==> Creating install root at /mnt
==> Installing packages to /mnt
:: Synchronizing package databases...
 core                                                 132.1 KiB  1247K/s 00:00 [#############################################] 100%
 extra                                               1641.7 KiB  4.62M/s 00:00 [#############################################] 100%
 community                                              4.9 MiB  3.02M/s 00:02 [#############################################] 100%
:: There are 50 members in group base:
:: Repository core
   1) bash  2) bzip2  3) coreutils  4) cryptsetup  5) device-mapper  6) dhcpcd  7) diffutils  8) e2fsprogs  9) file
   10) filesystem  11) findutils  12) gawk  13) gcc-libs  14) gettext  15) glibc  16) grep  17) gzip  18) inetutils  19) iproute2
   20) iputils  21) jfsutils  22) less  23) licenses  24) linux  25) linux-firmware  26) logrotate  27) lvm2  28) man-db
   29) man-pages  30) mdadm  31) nano  32) netctl  33) pacman  34) pciutils  35) perl  36) procps-ng  37) psmisc
   38) reiserfsprogs  39) s-nail  40) sed  41) shadow  42) sysfsutils  43) systemd-sysvcompat  44) tar  45) texinfo  46) usbutils
   47) util-linux  48) vi  49) which  50) xfsprogs

Enter a selection (default=all): 
:: There are 26 members in group base-devel:
:: Repository core
   1) autoconf  2) automake  3) binutils  4) bison  5) fakeroot  6) file  7) findutils  8) flex  9) gawk  10) gcc  11) gettext
   12) grep  13) groff  14) gzip  15) libtool  16) m4  17) make  18) pacman  19) patch  20) pkgconf  21) sed  22) sudo
   23) systemd  24) texinfo  25) util-linux  26) which

Enter a selection (default=all): 
warning: skipping target: file
warning: skipping target: findutils
warning: skipping target: gawk
warning: skipping target: gettext
warning: skipping target: grep
warning: skipping target: gzip
warning: skipping target: pacman
warning: skipping target: sed
warning: skipping target: texinfo
warning: skipping target: util-linux
warning: skipping target: which
resolving dependencies...
11) после заливки ОС нам нам создать файл в котором написано какие партиции при загрузке  монтировать, как и куда
root@archiso ~ # genfstab -p /mnt >> /mnt/etc/fstab     
12) Заходим в только что залитую ОС 
root@archiso ~ # arch-chroot /mntt
13) создаем имя нашей системы 
 echo name > /etc/hostname
14) Настраиваем часовой пояс
 ln -s /usr/share/zoneinfo/Asia/Yekaterinburg /etc/localtime
15) Добавим русскую локаль в систему
nano /etc/locale.gen
надо расскоментировать строки 
en_US.UTF-8 UTF-8
ru_RU.UTF-8 UTF-8
16) обновим локаль
locale-gen
[root@archiso /]# locale-gen
Generating locales...
  en_US.UTF-8... done
  ru_RU.UTF-8... done
Generation complete.
17) укажем язык системы
echo LANG="ru_RU.UTF-8" > /etc/locale.conf
18) Указываем keymap для console + прописываем шрифт
nano /etc/vconsole.conf
KEYMAP=ru
FONT=cyr-sun16
19) создадим загрузочный RAM диск
[root@archiso /]# mkinitcpio -p linux
==> Building image from preset: /etc/mkinitcpio.d/linux.preset: 'default'
  -> -k /boot/vmlinuz-linux -c /etc/mkinitcpio.conf -g /boot/initramfs-linux.img
==> Starting build: 5.3.1-arch1-1-ARCH
  -> Running build hook: [base]
  -> Running build hook: [udev]
  -> Running build hook: [autodetect]
  -> Running build hook: [modconf]
  -> Running build hook: [block]
  -> Running build hook: [filesystems]
  -> Running build hook: [keyboard]
  -> Running build hook: [fsck]
==> Generating module dependencies
==> Creating gzip-compressed initcpio image: /boot/initramfs-linux.img
==> Image generation successful
==> Building image from preset: /etc/mkinitcpio.d/linux.preset: 'fallback'
  -> -k /boot/vmlinuz-linux -c /etc/mkinitcpio.conf -g /boot/initramfs-linux-fallback.img -S autodetect
==> Starting build: 5.3.1-arch1-1-ARCH
  -> Running build hook: [base]
  -> Running build hook: [udev]
  -> Running build hook: [modconf]
  -> Running build hook: [block]
==> WARNING: Possibly missing firmware for module: aic94xx
==> WARNING: Possibly missing firmware for module: wd719x
  -> Running build hook: [filesystems]
  -> Running build hook: [keyboard]
  -> Running build hook: [fsck]
==> Generating module dependencies
==> Creating gzip-compressed initcpio image: /boot/initramfs-linux-fallback.img
==> Image generation successful
20) поменяем парот учетки root
[root@archiso /]# passwd
New password: 
Retype new password: 
passwd: password updated successfully
21) установим загрузчик и сетевой мэнеджер
[root@archiso /]# pacman -S grub efibootmgr networkmanager
resolving dependencies...
looking for conflicting packages...

Packages (22) bluez-libs-5.50-7  efivar-37-1  gpm-1.20.7.r27.g1fd1941-1  jansson-2.12-1  js60-60.9.0-1  libdaemon-0.14-4
              libmm-glib-1.10.6-1  libndp-1.7-1  libnewt-0.52.21-1  libnm-1.20.2-1  libpgm-5.2.122-3  libsodium-1.0.18-1
              libteam-1.29-1  nspr-4.22-1  nss-3.46-1  polkit-0.116-2  slang-2.3.2-1  wpa_supplicant-2:2.9-1  zeromq-4.3.2-1
              efibootmgr-16-1  grub-2:2.04-2  networkmanager-1.20.2-1

Total Download Size:    28.65 MiB
Total Installed Size:  141.07 MiB

:: Proceed with installation? [Y/n] y
:: Retrieving packages...
 grub-2:2.04-2-x86_64                                   6.1 MiB  4.31M/s 00:01 [#############################################] 100%
 efivar-37-1-x86_64                                    97.5 KiB   367K/s 00:00 [#############################################] 100%
 efibootmgr-16-1-x86_64                                19.3 KiB  0.00B/s 00:00 [#############################################] 100%
 nspr-4.22-1-x86_64                                   188.4 KiB  1963K/s 00:00 [#############################################] 100%
 nss-3.46-1-x86_64                                   1369.2 KiB  1809K/s 00:01 [#############################################] 100%
 wpa_supplicant-2:2.9-1-x86_64                        965.8 KiB  2.48M/s 00:00 [#############################################] 100%
 gpm-1.20.7.r27.g1fd1941-1-x86_64                     131.8 KiB  1967K/s 00:00 [#############################################] 100%
 libnm-1.20.2-1-x86_64                               1036.0 KiB  1679K/s 00:01 [#############################################] 100%
 js60-60.9.0-1-x86_64                                  13.2 MiB  3.24M/s 00:04 [#############################################] 100%
 polkit-0.116-2-x86_64                                386.8 KiB  2.97M/s 00:00 [#############################################] 100%
 libmm-glib-1.10.6-1-x86_64                           396.1 KiB  1584K/s 00:00 [#############################################] 100%
 slang-2.3.2-1-x86_64                                 714.1 KiB  1755K/s 00:00 [#############################################] 100%
 libndp-1.7-1-x86_64                                   17.0 KiB  4.15M/s 00:00 [#############################################] 100%
 libdaemon-0.14-4-x86_64                               18.2 KiB  0.00B/s 00:00 [#############################################] 100%
 libteam-1.29-1-x86_64                                256.6 KiB  1329K/s 00:00 [#############################################] 100%
 bluez-libs-5.50-7-x86_64                              70.7 KiB  1179K/s 00:00 [#############################################] 100%
 networkmanager-1.20.2-1-x86_64                         2.8 MiB  1935K/s 00:01 [#############################################] 100%
 jansson-2.12-1-x86_64                                 38.9 KiB  12.7M/s 00:00 [#############################################] 100%
 libnewt-0.52.21-1-x86_64                              89.5 KiB   570K/s 00:00 [#############################################] 100%
 libsodium-1.0.18-1-x86_64                            158.6 KiB  1635K/s 00:00 [#############################################] 100%
 libpgm-5.2.122-3-x86_64                              172.1 KiB  1851K/s 00:00 [#############################################] 100%
 zeromq-4.3.2-1-x86_64                                550.4 KiB  1759K/s 00:00 [#############################################] 100%
(22/22) checking keys in keyring                                               [#############################################] 100%
(22/22) checking package integrity                                             [#############################################] 100%
(22/22) loading package files                                                  [#############################################] 100%
(22/22) checking for file conflicts                                            [#############################################] 100%
(22/22) checking available disk space                                          [#############################################] 100%
:: Processing package changes...
( 1/22) installing grub                                                        [#############################################] 100%
Generate your bootloader configuration with:
  grub-mkconfig -o /boot/grub/grub.cfg
Optional dependencies for grub
    freetype2: For grub-mkfont usage
    fuse2: For grub-mount usage
    dosfstools: For grub-mkrescue FAT FS and EFI support
    efibootmgr: For grub-install EFI support [pending]
    libisoburn: Provides xorriso for generating grub rescue iso using grub-mkrescue
    os-prober: To detect other OSes when generating grub.cfg in BIOS systems
    mtools: For grub-mkrescue FAT FS support
( 2/22) installing efivar                                                      [#############################################] 100%
( 3/22) installing efibootmgr                                                  [#############################################] 100%
( 4/22) installing nspr                                                        [#############################################] 100%
( 5/22) installing nss                                                         [#############################################] 100%
( 6/22) installing jansson                                                     [#############################################] 100%
( 7/22) installing libnm                                                       [#############################################] 100%
( 8/22) installing js60                                                        [#############################################] 100%
( 9/22) installing polkit                                                      [#############################################] 100%
(10/22) installing wpa_supplicant                                              [#############################################] 100%
(11/22) installing libmm-glib                                                  [#############################################] 100%
(12/22) installing slang                                                       [#############################################] 100%
(13/22) installing gpm                                                         [#############################################] 100%
(14/22) installing libnewt                                                     [#############################################] 100%
Optional dependencies for libnewt
    tcl: whiptcl support
    python: libnewt support with the _snack module
    python2: libnewt support with the _snack module
(15/22) installing libndp                                                      [#############################################] 100%
(16/22) installing libdaemon                                                   [#############################################] 100%
(17/22) installing libsodium                                                   [#############################################] 100%
(18/22) installing libpgm                                                      [#############################################] 100%
(19/22) installing zeromq                                                      [#############################################] 100%
(20/22) installing libteam                                                     [#############################################] 100%
(21/22) installing bluez-libs                                                  [#############################################] 100%
(22/22) installing networkmanager                                              [#############################################] 100%
Optional dependencies for networkmanager
    dnsmasq: connection sharing
    bluez: Bluetooth support
    ppp: dialup connection support
    modemmanager: cellular network support
    iwd: wpa_supplicant alternative
:: Running post-transaction hooks...
(1/6) Creating system user accounts...
(2/6) Reloading system manager configuration...
Running in chroot, ignoring request: daemon-reload
(3/6) Reloading device manager configuration...
Running in chroot, ignoring request.
(4/6) Arming ConditionNeedsUpdate...
(5/6) Reloading system bus configuration...
Running in chroot, ignoring request: try-reload-or-restart
(6/6) Updating the info directory file...
22) установим наш загрузчик 
[root@archiso /]# grub-install --target=x86_64-efi --removable
Installing for x86_64-efi platform.
Installation finished. No error reported.
23) создадим конфиг загрузчика 
[root@archiso /]# grub-mkconfig -o /boot/grub/grub.cfg
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-linux
Found initrd image: /boot/initramfs-linux.img
Found fallback initrd image(s) in /boot: initramfs-linux-fallback.img
done

