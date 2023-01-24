# igorborodko_infra
igorborodko Infra repository

----
# Реализуем простой плейбук,changed=0 потому как ранее делали клонирование.
house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible-playbook clone.yml

PLAY [Clone] ******************************************************************************************************************************
TASK [Gathering Facts] ********************************************************************************************************************ok: [appserver]

TASK [Clone repo] *************************************************************************************************************************ok: [appserver]

PLAY RECAP ********************************************************************************************************************************appserver                 
 : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


# Выполнили удаление дирректории
house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible app -m command -a 'rm -rf ~/reddit'
appserver | CHANGED | rc=0 >>

# Клонировалась дирректория, файлы записались с нуля - changed=1.

house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible-playbook clone.yml

PLAY [Clone] ******************************************************************************************************************************
TASK [Gathering Facts] ********************************************************************************************************************ok: [appserver]

TASK [Clone repo] *************************************************************************************************************************changed: [appserver]

PLAY RECAP ********************************************************************************************************************************appserver               
: ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

# Задание со ⭐
# Чем отличаются статический и динамический инвентори я понял, но как настраиваются не разобрался.






# Ниже описание шагов основного задания.
----

# Создал инвентори
root@Igor:/home/house/GitHub/igorborodko_infra/ansible-1# nano inventory
appserver ansible_host=51.250.65.145 ansible_user=ubuntu \ ansible_private_key_file=~/home/house/.ssh/id_rsa
dbserver ansible_host=51.250.95.201 ansible_user=ubuntu \ ansible_private_key_file=~/home/house/.ssh/id_rsa


# ПРоверил команду пинг до appserver & dbserver 

house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible appserver -i ./inventory -m ping
The authenticity of host '51.250.65.145 (51.250.65.145)' can't be established.
ECDSA key fingerprint is SHA256:lSB/e1/rwT6jSv3JEGrUXFjGlpu6I3IBjgxioqLYbuc.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
appserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}


house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible dbserver -i ./inventory -m ping
The authenticity of host '51.250.95.201 (51.250.95.201)' can't be established.
ECDSA key fingerprint is SHA256:lVb7VHPczS4Pm7d5r5j9Ihepn9pLA0YvfRmzC8mSmHI.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
dbserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}


# Создал nano ansible.cfg
[defaults]
inventory = ./inventory
remote_user = ubuntu
private_key_file = ~/.ssh/id_rsa
host_key_checking = False
retry_files_enabled = False


# Проверил работу конфига
house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible appserver -m command -a uptime
appserver | CHANGED | rc=0 >>
 18:45:52 up  1:00,  1 user,  load average: 0.02, 0.01, 0.00
 
 

# Прописал группы - ОК
-----
house@Igor:~/GitHub/igorborodko_infra/ansible-1$ cat inventory
[app]
appserver ansible_host=51.250.65.145
#ansible_user=ubuntu \ ansible_private_key_file=~/home/house/.ssh/id_rsa
[db]
dbserver ansible_host=51.250.95.201
#ansible_user=ubuntu \ ansible_private_key_file=~/home/house/.ssh/id_rsa

-----
house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible app -m ping
appserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}


# Проверил ямл конфиг
house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible all -m ping -i inventory.yml
dbserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
appserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}


# Проверим, что на app сервере установлены компоненты для работы
#  приложения ( ruby и bundler ):
house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible app -m command -a 'ruby -v'
appserver | CHANGED | rc=0 >>
ruby 2.3.1p112 (2016-04-26) [x86_64-linux-gnu]

house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible app -m command -a 'bundler -v'
appserver | CHANGED | rc=0 >>
Bundler version 1.11.2

# Установил гит на оба хоста
house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible db -m shell -a 'git --version'
dbserver | CHANGED | rc=0 >>
git version 2.7.4

house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible app -m shell -a 'git --version'
appserver | CHANGED | rc=0 >>
git version 2.7.4


# Реализуем простой плейбук,changed=0 потому как ранее делали клонирование.
house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible-playbook clone.yml

PLAY [Clone] ******************************************************************************************************************************
TASK [Gathering Facts] ********************************************************************************************************************ok: [appserver]

TASK [Clone repo] *************************************************************************************************************************ok: [appserver]

PLAY RECAP ********************************************************************************************************************************appserver                 
 : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


# Выполнили удаление дирректории
house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible app -m command -a 'rm -rf ~/reddit'
appserver | CHANGED | rc=0 >>

# Клонировалась дирректория, файлы записались с нуля - changed=1.

house@Igor:~/GitHub/igorborodko_infra/ansible-1$ ansible-playbook clone.yml

PLAY [Clone] ******************************************************************************************************************************
TASK [Gathering Facts] ********************************************************************************************************************ok: [appserver]

TASK [Clone repo] *************************************************************************************************************************changed: [appserver]

PLAY RECAP ********************************************************************************************************************************appserver               
: ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0



