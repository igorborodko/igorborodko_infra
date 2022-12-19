# igorborodko_infra
igorborodko Infra repository

#Создал инстансы

bastion_IP = 35.198.167.169
someinternalhost_IP = 10.156.0.3

#Самостоятельное задание
house@Igor:/etc/ssh$ ssh -J appuser@35.198.167.169 appuser@10.128.0.29


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

#Прописал alias someinternalhost в nano ~/.bashrc
alias someinternalhost='ssh -J appuser@35.198.167.169 appuser@10.128.0.29'

#Применил конфиг
source ~/.bashrc

--------------------------------------------------------------------------------------------

#Добавил мощностей машине.
#Получил ключ
appuser@bastion:~$ sudo pritunl setup-key
5dd732569e484c61a90cc359550003b5

#Получил креды
appuser@bastion:~$ sudo pritunl default-password
Administrator default password:
  username: "pritunl"
  password: "pmkCcQyDaXID"
  
#Создал OVPN подключение, подключился с локальной машины к someinternalhost
 house@Igor:~$ ssh appuser@10.128.0.29

