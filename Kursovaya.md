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
6) После установки пакетов, начинаем создавать новый конфиг ядра, на основании старого
  root@localhost ~/linux-5.4.3# make oldconfig
6.1) Во время создания конфигурации потребуется установить пакеты которые запросит компилятор, пример:
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
6.2) Как мы можем видеть конфигуратор make не находит пакета flex 
      /bin/sh: flex: command not found
     Соответствеено нам нужно скачать и установить его 
7) После того, как когфигурация соберется, терминал поприветствует вас окном с просьбой выбрать параметры конфигурации
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
7.1) Оставляем все как есть, если кто то хочет изменить конфигурацию, надо читать документы чтобы понимать что вы включаете или выключаете https://www.kernel.org/doc/
8) После успешного создания конфига, начинаем сборку ядра в 4 потока 
root@localhost ~/linux-5.4.3# time make -j4




