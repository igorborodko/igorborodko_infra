# igorborodko_infra
igorborodko Infra repository

Задания выполнил, кроме ДЗ с звездочкой.

#Создал два образа

root@Igor:/home/house/GitHub/igorborodko_infra/packer# nano db.json
{
    "builders": [
        {
            "type": "yandex",
            "service_account_key_file": "/home/house/GitHub/igorborodko_infra/packer/key.json",
            "folder_id": "b1gb2j0k0cdnboead62b",
            "source_image_family": "ubuntu-1604-lts",
            "image_name": "reddit-base-{{timestamp}}",
            "image_family": "reddit-base",
            "ssh_username": "ubuntu",
            "use_ipv4_nat": true,
            "platform_id": "standard-v1"
        }
        ],
    "provisioners": [
        {
            "type": "shell",
            "script": "scripts/install_mongodb.sh",
            "execute_command": "sudo {{.Path}}"
        }
    ]
}

root@Igor:/home/house/GitHub/igorborodko_infra/packer# nano app.json
{
    "builders": [
        {
            "type": "yandex",
            "service_account_key_file": "/home/house/GitHub/igorborodko_infra/packer/key.json",
            "folder_id": "b1gb2j0k0cdnboead62b",
            "source_image_family": "ubuntu-1604-lts",
            "image_name": "reddit-base-{{timestamp}}",
            "image_family": "reddit-base",
            "ssh_username": "ubuntu",
            "use_ipv4_nat": true,
            "platform_id": "standard-v1"
        }
        ],
    "provisioners": [
        {
            "type": "shell",
            "script": "scripts/install_ruby.sh",
            "execute_command": "sudo {{.Path}}"
        }
    ]
}


#Создались две ВМ
Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

yandex_compute_instance.app: Creating...
yandex_compute_instance.db: Creating...
yandex_compute_instance.app: Still creating... [10s elapsed]
yandex_compute_instance.db: Still creating... [10s elapsed]
yandex_compute_instance.app: Still creating... [20s elapsed]
yandex_compute_instance.db: Still creating... [20s elapsed]
yandex_compute_instance.app: Still creating... [30s elapsed]
yandex_compute_instance.db: Still creating... [30s elapsed]
yandex_compute_instance.app: Still creating... [40s elapsed]
yandex_compute_instance.db: Still creating... [40s elapsed]
yandex_compute_instance.app: Still creating... [50s elapsed]
yandex_compute_instance.db: Still creating... [50s elapsed]
yandex_compute_instance.db: Still creating... [1m0s elapsed]
yandex_compute_instance.app: Still creating... [1m0s elapsed]
yandex_compute_instance.app: Creation complete after 1m3s [id=fhm86l9t605703aurbdm]
yandex_compute_instance.db: Creation complete after 1m4s [id=fhm68no2dgfroi66bril]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

external_ip_address_app = 158.160.35.34
external_ip_address_db = 158.160.54.127

# Подключился к обоим, ПО установлено
ubuntu@fhm86l9t605703aurbdm:~$ ruby --version
ruby 2.3.1p112 (2016-04-26) [x86_64-linux-gnu]

ubuntu@fhm68no2dgfroi66bril:~$ systemctl status mongod
● mongod.service - MongoDB Database Server
   Loaded: loaded (/lib/systemd/system/mongod.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2023-01-22 13:17:28 UTC; 7min ago
     Docs: https://docs.mongodb.org/manual
 Main PID: 657 (mongod)
   CGroup: /system.slice/mongod.service
           └─657 /usr/bin/mongod --config /etc/mongod.conf

Jan 22 13:17:28 fhm68no2dgfroi66bril systemd[1]: Started MongoDB Database Server.


# Была ошибка при иницализации модулей в папках stage prod -  решилась при подклчюеном ВПН
root@Igor:/home/house/GitHub/igorborodko_infra/terraform-2/prod# terraform init
Initializing modules...

Initializing the backend...

Initializing provider plugins...
- Checking for available provider plugins...

Registry service unreachable.

This may indicate a network issue, or an issue with the requested Terraform Registry.


Error: registry service is unreachable, check https://status.hashicorp.com/ for status updates