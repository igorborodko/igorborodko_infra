# igorborodko_infra
igorborodko Infra repository

# Создал playbook (изменен - итоговый)
house@Igor:~/GitHub/igorborodko_infra/ansible-2$ cat reddit_app.yml
 - name: Configure hosts & deploy application
    hosts: all
    vars:
      mongo_bind_ip: 0.0.0.0 # <-- Переменная задается в блоке vars
      db_host: 10.128.0.5 # <-- подставьте сюда ваш IP

    tasks:
    - name: Change mongo config file
      become: true
      template:
        src: templates/mongod.conf.j2
        dest: /etc/mongod.conf
        mode: 0644
      tags: db-tag # <-- Список тэгов для задачи
      notify: restart mongod
    - name: Add unit file for Puma
      become: true
      copy:
        src: files/puma.service
        dest: /etc/systemd/system/puma.service
      tags: app-tag
      notify: reload puma
    - name: Add config for DB connection
      template:
        src: templates/db_config.j2
        dest: /home/ubuntu/db_config
      tags: app-tag
    - name: enable puma
      become: true
      systemd: name=puma enabled=yes
      tags: app-tag   
    handlers: # <-- Добавим блок handlers и задачу
    - name: restart mongod
      become: true
      service: name=mongod state=restarted
    - name: reload puma
      become: true
      systemd: name=puma state=restarted
	 

# Делаем проверку плейбука
house@Igor:~/GitHub/igorborodko_infra/ansible-2$ ansible-playbook reddit_app.yml --check --limit db

PLAY [Configure hosts & deploy application] ***********************************************************************************************
TASK [Gathering Facts] ********************************************************************************************************************ok: [dbserver]

TASK [Change mongo config file] ***********************************************************************************************************changed: [dbserver]

PLAY RECAP ********************************************************************************************************************************dbserver                   
: ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0



# Создал puma.service
root@Igor:/home/house/GitHub/igorborodko_infra/ansible-2/files# cat puma.service
[Unit]
Description=Puma HTTP Server
After=network.target

[Service]
Type=simple
EnvironmentFile=/home/ubuntu/db_config
User=ubuntu
WorkingDirectory=/home/ubuntu/reddit
ExecStart=/bin/bash -lc 'puma'
Restart=always

[Install]
WantedBy=multi-user.target


# Столкнулся с ошибкой на Настройке инстанса приложения
# FAILED! => {"changed": false, "msg": "Could not find the requested service puma: host"}
# Решилась выдачей прав puma и убрал --check!!!

house@Igor:~/GitHub/igorborodko_infra/ansible-2$ ansible-playbook reddit_app.yml --check --limit app --tags app-tag

PLAY [Configure hosts & deploy application] ***********************************************************************************************
TASK [Gathering Facts] ********************************************************************************************************************ok: [appserver]

TASK [Add unit file for Puma] *************************************************************************************************************changed: [appserver]

TASK [Add config for DB connection] *******************************************************************************************************changed: [appserver]

TASK [enable puma] ************************************************************************************************************************fatal: [appserver]: FAILED! => {"changed": false, "msg": "Could not find the requested service puma: host"}

RUNNING HANDLER [reload puma] *************************************************************************************************************
PLAY RECAP ********************************************************************************************************************************appserver                  
: ok=3    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0

# Сервис на месте
house@Igor:~/GitHub/igorborodko_infra/ansible-2$ tree
.
├── ansible.cfg
├── clone.yml
├── files
│   └── puma.service
├── inventory
├── inventory.yml

# Решилась выдачей прав puma  и убрал --check !!!
# Done
PLAY RECAP ********************************************************************************************************************************appserver                  
: ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


Закончил Деплой 35 стр.

# Поправил db_host: 158.160.45.53 в reddit_app.yml


# Запустил плэйбук DB  - OK
house@Igor:~/GitHub/igorborodko_infra/ansible-2$ ansible-playbook reddit_app.yml --limit db

PLAY [Configure hosts & deploy application] *****************************************************************************************************************
TASK [Gathering Facts] **************************************************************************************************************************************ok: [dbserver]

TASK [Change mongo config file] *****************************************************************************************************************************ok: [dbserver]

TASK [Add unit file for Puma] *******************************************************************************************************************************changed: [dbserver]

TASK [Add config for DB connection] *************************************************************************************************************************changed: [dbserver]

TASK [enable puma] ******************************************************************************************************************************************changed: [dbserver]

TASK [Fetch the latest version of application code] *********************************************************************************************************changed: [dbserver]

TASK [Bundle install] ***************************************************************************************************************************************changed: [dbserver]

RUNNING HANDLER [reload puma] *******************************************************************************************************************************changed: [dbserver]

PLAY RECAP **************************************************************************************************************************************************dbserver                 
  : ok=8    changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


# Проверяем работу приложения - ОК



# ПРоверяем работу с несколькими плейбуками

house@Igor:~/GitHub/igorborodko_infra/ansible-2$ ansible-playbook site.yml

PLAY [Configure MongoDB] ************************************************************************************************************
TASK [Gathering Facts] **************************************************************************************************************ok: [dbserver]

TASK [Change mongo config file] *****************************************************************************************************changed: [dbserver]

RUNNING HANDLER [restart mongod] ****************************************************************************************************changed: [dbserver]

PLAY [Configure App] ****************************************************************************************************************
TASK [Gathering Facts] **************************************************************************************************************ok: [appserver]

TASK [Add unit file for Puma] *******************************************************************************************************changed: [appserver]

TASK [Add config for DB connection] *************************************************************************************************changed: [appserver]

TASK [enable puma] ******************************************************************************************************************changed: [appserver]

RUNNING HANDLER [reload puma] *******************************************************************************************************changed: [appserver]

PLAY [Deploy App] *******************************************************************************************************************
TASK [Gathering Facts] **************************************************************************************************************ok: [appserver]

TASK [Fetch the latest version of application code] *********************************************************************************ok: [appserver]

TASK [bundle install] ***************************************************************************************************************ok: [appserver]

PLAY RECAP **************************************************************************************************************************appserver                  : ok=8    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
dbserver                   : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0