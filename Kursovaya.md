1) Для примера берем OS CentOS 7, настроиваем на VirtualBox общую сеть и подключаемся по ssh к нашей созданной виртуалке
┬─[mickwild@ISeeYou:~]─[17:15:48]
╰─>$ ssh root@192.168.42.99

2) Скачиваем с Kernel.org постеледнюю ванильную версию ядра https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.3.tar.xz

root@localhost ~# wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.3.tar.xz
--2019-12-16 17:17:50--  https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.3.tar.xz
Resolving cdn.kernel.org (cdn.kernel.org)... 151.101.113.176, 2a04:4e42:1b::432
Connecting to cdn.kernel.org (cdn.kernel.org)|151.101.113.176|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 109443116 (104M) [application/x-xz]
Saving to: ‘linux-5.4.3.tar.xz’

100%[===================================================>] 109 443 116 1,35MB/s   in 66s    

2019-12-16 17:18:56 (1,59 MB/s) - ‘linux-5.4.3.tar.xz’ saved [109443116/109443116]

3) Разархивируем только что скачанный архив 
root@localhost ~# tar -xvf linux-5.4.3.tar.xz 
4) Переходим в директорию разархивированного ядра, копируем туда конфигурацию старого ядра, для того чтобы на основе него скомпилировать новое ядро
  root@localhost ~/linux-5.4.3# cd ./linux-5.4.3 && cp /boot/config-3.10.0-1062.9.1.el7.x86_64 .config 
5) Для компиляции ядра, нам потребуется скачать и установить 2 пакета make и gcc



