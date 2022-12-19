# igorborodko_infra
igorborodko Infra repository

#Создал инстансы
house@Igor:~/.ssh$ yc compute instance list
+----------------------+------------------+---------------+---------+---------------+-------------+
|          ID          |       NAME       |    ZONE ID    | STATUS  | EXTERNAL IP   | INTERNAL IP |
+----------------------+------------------+---------------+---------+---------------+-------------+
| fhmifbo24osbvuhtbn2t | someinternalhost | ru-central1-a | RUNNING |               | 10.128.0.29 |
| fhmtcefoo09ds199pa4b | bastion          | ru-central1-a | RUNNING |35.198.167.169 | 10.128.0.3  |
+----------------------+------------------+---------------+---------+---------------+-------------+

bastion_IP = 35.198.167.169
someinternalhost_IP = 10.156.0.3

#Самостоятельное задание
house@Igor:/etc/ssh$ ssh -J appuser@35.198.167.169 appuser@10.128.0.29
The authenticity of host '10.128.0.29 (<no hostip for proxy command>)' can't be established.
ECDSA key fingerprint is SHA256:1mFEWZDeqnsog0HNoVQlkoKRYu/0Erf6EKwuet1Laps.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.128.0.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.4.0-132-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Sun Dec 18 19:12:03 2022 from 10.128.0.3

#ПРоверка хоста
appuser@someinternalhost:~$ hostname
someinternalhost

appuser@someinternalhost:~$  ip a show eth0
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether d0:0d:12:7a:f0:22 brd ff:ff:ff:ff:ff:ff
    inet 10.128.0.29/24 brd 10.128.0.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::d20d:12ff:fe7a:f022/64 scope link
       valid_lft forever preferred_lft forever

--------------------------------------------------------------------------------------------

#Доп задание для сквозного подулючения

#Прописал alias в nano ~/.bashrc
alias someinternalhost='ssh -J appuser@35.198.167.169 appuser@10.128.0.29'

#Применил конфиг
source ~/.bashrc

#Проверил конфиг
house@Igor:/etc/ssh$ alias
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'
alias someinternalhost='ssh -J appuser@35.198.167.169 appuser@10.128.0.29'

#Проверил сквозное подключение с локальной машины
house@Igor:/etc/ssh$ someinternalhost
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.4.0-132-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Sun Dec 18 19:42:53 2022 from 10.128.0.3

#проверил хост 
appuser@someinternalhost:~$ hostname
someinternalhost

--------------------------------------------------------------------------------------------

#Добавил мощностей машине.
#Получил ключ
appuser@bastion:~$ sudo pritunl setup-key
5dd732569e484c61a90cc359550003b5


#Получил креды
appuser@bastion:~$ sudo pritunl default-password
[undefined][2022-12-19 18:28:54,283][INFO] Getting default administrator password
Administrator default password:
  username: "pritunl"
  password: "pmkCcQyDaXID"
  
#Создал OVPN подключение, подключился с локальной машины к someinternalhost
 house@Igor:~$ ssh appuser@10.128.0.29
Welcome to Ubuntu 20.04.5 LTS (GNU/Linux 5.4.0-132-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Sun Dec 18 20:23:03 2022 from 10.128.0.3

appuser@someinternalhost:~$
