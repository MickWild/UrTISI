
1) Обновляем систему с помощью менеджера пакетов вашей ос apt\yum\dnf\zypper
2) После обновления пакетов, установливаем с помощью менеджера пакетов ssh client и ssh server
```bash
[root@localhost ~]# dnf update -y && dnf install openssh-server
```
3) Переходим в директорию демона ssh и с помощью vi открываем и смотрим примерный файл конфигурации sshd_server
```bash
[root@localhost ~]# cd /etc/ssh/
[root@localhost ssh]# ls -l
total 588
-rw-r--r--. 1 root root     563386 May 11  2019 moduli
-rw-r--r--. 1 root root       1727 May 11  2019 ssh_config
drwxr-xr-x. 2 root root         28 Dec 18 05:52 ssh_config.d
-rw-r-----. 1 root ssh_keys    492 Dec 18 06:59 ssh_host_ecdsa_key
-rw-r--r--. 1 root root        162 Dec 18 06:59 ssh_host_ecdsa_key.pub
-rw-r-----. 1 root ssh_keys    387 Dec 18 06:59 ssh_host_ed25519_key
-rw-r--r--. 1 root root         82 Dec 18 06:59 ssh_host_ed25519_key.pub
-rw-r-----. 1 root ssh_keys   1799 Dec 18 06:59 ssh_host_rsa_key
-rw-r--r--. 1 root root        382 Dec 18 06:59 ssh_host_rsa_key.pub
-rw-------. 1 root root       4444 May 11  2019 sshd_config

[root@localhost ssh]# vi sshd_config 
#	$OpenBSD: sshd_config,v 1.103 2018/04/09 20:41:22 tj Exp $

# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.

# This sshd was compiled with PATH=/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin

# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value.

# If you want to change the port on a SELinux system, you have to tell
# SELinux about this change.
# semanage port -a -t ssh_port_t -p tcp #PORTNUMBER
#
#Port 22 - порт который слушается для соединения, можно использовать несколько
#AddressFamily any - 
#ListenAddress 0.0.0.0 - слушает только конкретный адрес ipv4 
#ListenAddress :: - слушает только конкретный адрес ipv6

HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key

# Ciphers and keying
#RekeyLimit default none

# System-wide Crypto policy:
# This system is following system-wide crypto policy. The changes to
# Ciphers, MACs, KexAlgoritms and GSSAPIKexAlgorithsm will not have any
# effect here. They will be overridden by command-line options passed on
# the server start up.
# To opt out, uncomment a line with redefinition of  CRYPTO_POLICY=
# variable in  /etc/sysconfig/sshd  to overwrite the policy.
# For more information, see manual page for update-crypto-policies(8).

# Logging
#SyslogFacility AUTH
SyslogFacility AUTHPRIV
#LogLevel INFO - уровень логирования

# Authentication:

#LoginGraceTime 2m - таймаут ожидания
PermitRootLogin yes - разрешить соединение под root
#StrictModes yes
#MaxAuthTries 6 - максимальное количество ошибочно введенных паролей
#MaxSessions 10 -  максимальное количество сессий

#PubkeyAuthentication yes -аутентификация на основе ssh2 rsa ключей

# The default is to check both .ssh/authorized_keys and .ssh/authorized_keys2
# but this is overridden so installations will only check .ssh/authorized_keys
AuthorizedKeysFile	.ssh/authorized_keys - файл содержащий разрешенные ключи для подключения

#AuthorizedPrincipalsFile none

#AuthorizedKeysCommand none
#AuthorizedKeysCommandUser nobody

# For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
#HostbasedAuthentication no
# Change to yes if you don't trust ~/.ssh/known_hosts for
# HostbasedAuthentication
#IgnoreUserKnownHosts no
# Don't read the user's ~/.rhosts and ~/.shosts files
#IgnoreRhosts yes

# To disable tunneled clear text passwords, change to no here!
#PasswordAuthentication yes - аутентификация с помощью пароля
#PermitEmptyPasswords no - разрешить пустые пароли
PasswordAuthentication yes - аутентификация с помощью пароля

# Change to no to disable s/key passwords
#ChallengeResponseAuthentication yes
ChallengeResponseAuthentication no

# Kerberos options
#KerberosAuthentication no - доменная аутентификация
#KerberosOrLocalPasswd yes -доменная аутентификация с помощью локального пароля
#KerberosTicketCleanup yes
#KerberosGetAFSToken no
#KerberosUseKuserok yes

# GSSAPI options
GSSAPIAuthentication yes
GSSAPICleanupCredentials no
#GSSAPIStrictAcceptorCheck yes
#GSSAPIKeyExchange no
#GSSAPIEnablek5users no

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin without-password".
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'.
# WARNING: 'UsePAM no' is not supported in Fedora and may cause several
# problems.
UsePAM yes

#AllowAgentForwarding yes
#AllowTcpForwarding yes
#GatewayPorts no
X11Forwarding yes - перенаправление на X11 если сервер установлен
#X11DisplayOffset 10
#X11UseLocalhost yes
#PermitTTY yes

# It is recommended to use pam_motd in /etc/pam.d/sshd instead of PrintMotd,
# as it is more configurable and versatile than the built-in version.
PrintMotd no

#PrintLastLog yes
#TCPKeepAlive yes
#PermitUserEnvironment no
#Compression delayed
#ClientAliveInterval 0 - время бездействия клиента
#ClientAliveCountMax 3 - 
#ShowPatchLevel no
#UseDNS no
#PidFile /var/run/sshd.pid
#MaxStartups 10:30:100
#PermitTunnel no
#ChrootDirectory none
#VersionAddendum none

# no default banner path
#Banner none - приветствие при подключении

# Accept locale-related environment variables
AcceptEnv LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES
AcceptEnv LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT
AcceptEnv LC_IDENTIFICATION LC_ALL LANGUAGE
AcceptEnv XMODIFIERS

# override default of no subsystems
Subsystem	sftp	/usr/libexec/openssh/sftp-server -  подсистема sftp 

# Example of overriding settings on a per-user basis
#Match User anoncvs
#	X11Forwarding no
#	AllowTcpForwarding no
#	PermitTTY no
#	ForceCommand cvs server
```
4) После изучения конфигурации закрываем ее, запускаем ssh-server и пытаемся подключиться по root или другой уз которая там присутствует
```bash
[root@localhost ~]# systemctl start sshd

┬─[mickwild@ISeeYou:~]─[11:20:39]
╰─>$ ssh root@192.168.42.219
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Wed Dec 18 23:47:12 2019 from 192.168.42.181
[root@localhost ~]# 
```
5) 
