# igorborodko_infra
igorborodko Infra repository



#Создал ВМ из образа
Доступна по http://158.160.36.205:9292/

Сделал  variables.json.examples 

Подробнее ниже.
---------------------------------------------------------------
root@Igor:/# yc config list
token: AQAAAAAC-neIAATuwcF84NEJVULYtlekoVh1XWU
cloud-id: b1gi2jp8jll6adpg1frh
folder-id: b1gb2j0k0cdnboead62b
compute-default-zone: ru-central1-a

#Создайл сервисный аккаунт:
$ SVC_ACCT="house-srv"
$ FOLDER_ID="b1gb2j0k0cdnboead62b"
$ yc iam service-account create --name house-srv --folder-id b1gb2j0k0cdnboead62b


#Выдайте права аккаунту:
root@Igor:/home/house# ACCT_ID=$(yc iam service-account get $SVC_ACCT | \
ep ^id > grep ^id | \
> awk '{print $2}')

root@Igor:/home/house# yc resource-manager folder add-access-binding --id $FOLDER_ID \
> --role editor \
> --service-account-id $ACCT_ID
done (1s)

#Проверка шаблона
root@Igor:/home/house/GitHub/igorborodko_infra/packer# packer validate ./ubuntu16.json
The configuration is valid.

#Запуск сборки
root@Igor:/home/house/GitHub/igorborodko_infra/packer#  packer build ./ubuntu16.json

#Получаем ошибку 
Build 'yandex' errored: Failed to find instance ip address:
instance has no one IPv4 external address

#Ошибка IPv4 - исправление
Добавил в скрипт ubuntu16.json  "use_ipv4_nat": true

#Создал образ диска
==> Wait completed after 4 minutes 8 seconds

==> Builds finished. The artifacts of successful builds are:
--> yandex: A disk image was created: reddit-base-1673083636 (id: fd8ba6vtj6t87pn8bs1m) with family name reddit-base

#Создал ВМ из образа
Доступна по http://158.160.36.205:9292/

house@Igor:~/.ssh$ yc compute image list
+----------------------+------------------------+-------------+----------------------+--------+
|          ID          |          NAME          |   FAMILY    |     PRODUCT IDS      | STATUS |
+----------------------+------------------------+-------------+----------------------+--------+
| fd8ba6vtj6t87pn8bs1m | reddit-base-1673083636 | reddit-base | f2e1d3peer6s963qe0se | READY  |
+----------------------+------------------------+-------------+----------------------+--------+

There is a new yc version '0.100.0' available. Current version: '0.91.0'.
See release notes at https://cloud.yandex.ru/docs/cli/release-notes
You can install it by running the following command in your shell:
        $ yc components update