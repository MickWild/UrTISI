1) Для примера берем OS CentOS 7, настроиваем на VirtualBox общую сеть и подключаемся по ssh к нашей созданной виртуалке

```consocle
┬─[mickwild@ISeeYou:~]─[17:15:48]
╰─>$ ssh root@192.168.42.99
```
2) Скачиваем с Kernel.org постеледнюю ванильную версию ядра https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.3.tar.xz
```console
root@localhost ~# wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.3.tar.xz
--2019-12-16 17:17:50--  https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.4.3.tar.xz
Resolving cdn.kernel.org (cdn.kernel.org)... 151.101.113.176, 2a04:4e42:1b::432
Connecting to cdn.kernel.org (cdn.kernel.org)|151.101.113.176|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 109443116 (104M) [application/x-xz]
Saving to: ‘linux-5.4.3.tar.xz’

100%[===================================================>] 109 443 116 1,35MB/s   in 66s    

2019-12-16 17:18:56 (1,59 MB/s) - ‘linux-5.4.3.tar.xz’ saved [109443116/109443116]
```
3) Разархивируем только что скачанный архив 
```console
root@localhost ~# tar -xvf linux-5.4.3.tar.xz 
```
4) Переходим в директорию разархивированного ядра, копируем туда конфигурацию старого ядра, для того чтобы на основе него скомпилировать новое ядро
```console
root@localhost ~/linux-5.4.3# cd ./linux-5.4.3 && cp /boot/config-3.10.0-1062.9.1.el7.x86_64 .config
```
5) Для компиляции ядра, нам потребуется скачать и установить 2 пакета make и gcc
6) После установки пакетов, начинаем создавать новый конфиг ядра, на основании старого
```console
root@localhost ~/linux-5.4.3# make oldconfig
```
  6.1) Во время создания конфигурации потребуется установить пакеты которые запросит компилятор, пример:
```console
root@localhost ~/linux-5.4.3# make oldconfig
  HOSTCC  scripts/basic/fixdep
  HOSTCC  scripts/kconfig/conf.o
  HOSTCC  scripts/kconfig/confdata.o
  HOSTCC  scripts/kconfig/expr.o
  LEX     scripts/kconfig/lexer.lex.c
/bin/sh: flex: command not found
make[2]: *** [scripts/kconfig/lexer.lex.c] Error 127
make[1]: *** [oldconfig] Error 2
make: *** [sub-make] Error 2
```
   6.2) Как мы можем видеть конфигуратор make не находит пакета flex 

```console
/bin/sh: flex: command not found
```
   Соответствеено нам нужно скачать и установить его 
   
7) После того, как когфигурация соберется, терминал поприветствует вас окном с просьбой выбрать параметры конфигурации
```console
root@localhost ~/linux-5.4.3# make oldconfig
  YACC    scripts/kconfig/parser.tab.[ch]
  HOSTCC  scripts/kconfig/lexer.lex.o
  HOSTCC  scripts/kconfig/parser.tab.o
  HOSTCC  scripts/kconfig/preprocess.o
  HOSTCC  scripts/kconfig/symbol.o
  HOSTLD  scripts/kconfig/conf
scripts/kconfig/conf  --oldconfig Kconfig
.config:695:warning: symbol value 'm' invalid for CPU_FREQ_STAT
.config:939:warning: symbol value 'm' invalid for NF_CT_PROTO_GRE
.config:967:warning: symbol value 'm' invalid for NF_NAT_REDIRECT
.config:970:warning: symbol value 'm' invalid for NF_TABLES_INET
.config:1137:warning: symbol value 'm' invalid for NF_TABLES_IPV4
.config:1141:warning: symbol value 'm' invalid for NF_TABLES_ARP
.config:1182:warning: symbol value 'm' invalid for NF_TABLES_IPV6
.config:1559:warning: symbol value 'm' invalid for NET_DEVLINK
.config:3661:warning: symbol value 'm' invalid for LIRC
*
* Restart config...
*
*
* General setup
*
Compile also drivers which will not load (COMPILE_TEST) [N/y/?] (NEW)
```
  7.1) Оставляем все как есть, если кто то хочет изменить конфигурацию, надо читать документы чтобы понимать что вы включаете или выключаете https://www.kernel.org/doc/
8) После успешного создания конфига, начинаем сборку ядра в 4 потока 
```console
root@localhost ~/linux-5.4.3# time make -j4
```
> Dec 16 16:48:44 Installed: mpfr-3.1.1-4.el7.x86_64
> Dec 16 16:48:44 Installed: libmpc-1.0.1-3.el7.x86_64
> Dec 16 16:48:44 Installed: cpp-4.8.5-39.el7.x86_64
> Dec 16 16:48:45 Installed: kernel-headers-3.10.0-1062.9.1.el7.x86_64
> Dec 16 16:48:45 Installed: glibc-headers-2.17-292.el7.x86_64
> Dec 16 16:48:45 Installed: glibc-devel-2.17-292.el7.x86_64
> Dec 16 16:48:46 Installed: gcc-4.8.5-39.el7.x86_64
> Dec 16 17:08:11 Installed: epel-release-7-11.noarch
> Dec 16 17:09:11 Installed: bc-1.06.95-13.el7.x86_64
> Dec 16 17:09:12 Installed: fish-2.3.1-2.el7.x86_64
> Dec 16 17:17:39 Installed: wget-1.14-18.el7_6.1.x86_64
> Dec 16 17:38:17 Installed: m4-1.4.16-10.el7.x86_64
> Dec 16 17:38:17 Installed: flex-2.5.37-6.el7.x86_64
> Dec 16 17:38:56 Installed: bison-3.0.4-2.el7.x86_64
> Dec 16 17:48:45 Installed: time-1.7-45.el7.x86_64
> Dec 16 17:58:56 Installed: keyutils-libs-devel-1.5.8-3.el7.x86_64
> Dec 16 17:58:56 Installed: libsepol-devel-2.5-10.el7.x86_64
> Dec 16 17:58:57 Installed: pcre-devel-8.32-17.el7.x86_64
> Dec 16 17:58:57 Installed: libselinux-devel-2.5-14.1.el7.x86_64
> Dec 16 17:58:57 Installed: libkadm5-1.15.1-37.el7_7.2.x86_64
> Dec 16 17:58:57 Installed: libcom_err-devel-1.42.9-16.el7.x86_64
> Dec 16 17:58:57 Installed: zlib-devel-1.2.7-18.el7.x86_64
> Dec 16 17:58:57 Installed: libverto-devel-0.2.5-4.el7.x86_64
> Dec 16 17:58:57 Installed: krb5-devel-1.15.1-37.el7_7.2.x86_64
> Dec 16 17:58:57 Installed: 1:openssl-devel-1.0.2k-19.el7.x86_64
> Dec 16 18:00:04 Installed: elfutils-libelf-devel-0.176-2.el7.x86_64
9) После успешной сборки ядра производим его установку

```console
root@localhost ~/linux-5.4.3# make install &&make modules_install
```

10) Сборка и установка закончены, теперь требуется обновить конфигурацию загрузчика grub для того чтобы он знал он новой версии нашего ядра и мы смогли с ним загрузиться

```console
grub2-mkconfig -o /boot/grub/grub.cfg
```
ключ "-o" обозначает папку и файл нового сгенерированного конфига загрузчика
После перезагрузки у вас должно быть на выбор 3-4 ядра, одно из которых будет ваше.

11) Далее нам требуется скачать файлы самого заббика, воспользуемся командой wget пример:

```console
wget https://repo.zabbix.com/zabbix/4.4/rhel/7/x86_64/zabbix-web-mysql-4.4.3-1.el7.noarch.rpm
```
Все необходимые пакеты для работы zabbix-server можно будет посмотреть на официальном сайте заббикс в документации по установке https://www.zabbix.com/documentation/current/manual или в видеогиде https://www.youtube.com/embed/yYmkFf3AEBo?autoplay=1

12) Чтобы установить пакеты, надо использовать менеджер пакетов 
на Red Hat rpm -i <название пакета>, 
на Debian dpkg -i <название пакета>
Во время установки пакетов приложений могут возникнуть проблемы в установке, проблемы возникают из-за неразрешенный зависимостей, соответственно надо установить недостающие пакеты. Пример: 
```console
warning: zabbix-agent-4.4.3-1.el7.x86_64.rpm: Header V4 RSA/SHA512 Signature, key ID a14fe591: NOKEY
error: Failed dependencies:
	java-headless >= 1.6.0 is needed by zabbix-java-gateway-4.4.3-1.el7.x86_64
	fping is needed by zabbix-server-mysql-4.4.3-1.el7.x86_64
	libOpenIPMI.so.0()(64bit) is needed by zabbix-server-mysql-4.4.3-1.el7.x86_64
	libOpenIPMIposix.so.0()(64bit) is needed by zabbix-server-mysql-4.4.3-1.el7.x86_64
	libevent-2.0.so.5()(64bit) is needed by zabbix-server-mysql-4.4.3-1.el7.x86_64
	libnetsnmp.so.31()(64bit) is needed by zabbix-server-mysql-4.4.3-1.el7.x86_64
	libodbc.so.2()(64bit) is needed by zabbix-server-mysql-4.4.3-1.el7.x86_64
	dejavu-sans-fonts is needed by zabbix-web-4.4.3-1.el7.noarch
	httpd is needed by zabbix-web-4.4.3-1.el7.noarch
	php >= 5.4 is needed by zabbix-web-4.4.3-1.el7.noarch
	php-bcmath is needed by zabbix-web-4.4.3-1.el7.noarch
	php-gd is needed by zabbix-web-4.4.3-1.el7.noarch
	php-ldap is needed by zabbix-web-4.4.3-1.el7.noarch
	php-mbstring is needed by zabbix-web-4.4.3-1.el7.noarch
	php-xml is needed by zabbix-web-4.4.3-1.el7.noarch
	php-mysql is needed by zabbix-web-mysql-4.4.3-1.el7.noarch
```
Название пакетов которых не хватает для zabbix'a
