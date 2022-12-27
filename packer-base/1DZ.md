
#Создайл сервисный аккаунт:
root@Igor:/# yc iam service-account create --name $SVC_ACCT --folder-id $FOLDER_ID
id: aje5g0mcrt705jceo3jk
folder_id: b1gb2j0k0cdnboead62b
created_at: "2022-12-26T20:00:43.423810382Z"
name: house-srv

ACCT_ID=$(yc iam service-account get $SVC_ACCT | \
grep ^id | \
awk '{print $2}')

#Создание service account key file (неверно задал путь)

root@Igor:/# yc iam key create --service-account-id $ACCT_ID --output C:\Users\house\OneDrive\Документы\iam_key\key.json
id: ajefjld45rqka47390e6
service_account_id: aje5g0mcrt705jceo3jk
created_at: "2022-12-26T20:12:23.089435610Z"
key_algorithm: RSA_2048

root@Igor:/# ls
C:UsershouseOneDriveДокументыiam_keykey.json

