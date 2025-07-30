

---

Процесс восстановления для mysql:

- Останавливаем mysql
- Удаляем данные из /var/lib/mysql
- Вызываем метод для загрузки бэкапа из svc-cloud-backup-agent (см. ниже) и на лету распаковываем полученный бэкап в /var/lib/mysql
- Вызываем xtrabackup --prepare --target-dir=/var/lib/mysql
- Вызываем xtrabackup --restore
- Делаем chown -R mysql:mysql /var/lib/mysql
- Запускаем mysql

Необходимо выполнить следующие шаги:
```
# Подготовить директорию для xtrabackup
gxzpukbewq ~ [0] # mkdir 10-unpacked
# Распаковать сжатый стрим в директорию
gxzpukbewq ~ [0] # zcat 10 | xbstream -x -C 10-unpacked/
# Выполнить подготовку файлов к восстановлению с помощью xtrabackup
gxzpukbewq ~ [0] # xtrabackup --prepare --target-dir=./10-unpacked/
# Остановить MySQL
gxzpukbewq ~ [0] # systemctl stop mysql.service
# Удалить текущие файлы MySQL и создать вместо этого пустую директорию
gxzpukbewq ~ [0] # rm -rf /var/lib/mysql
gxzpukbewq ~ [0] # mkdir /var/lib/mysql
# Скопировать файлы из директории с копией
gxzpukbewq ~ [0] # xtrabackup --copy-back --target-dir=./10-unpacked/
# Исправить владельца директории и файлов
gxzpukbewq ~ [0] # chown -R mysql:mysql /var/lib/mysql
# Запустить MySQL
gxzpukbewq ~ [0] # systemctl start mysql.service
```

Если бэкап восстанавливается на другую впску:
* после восстановления нужно сначала запустить mysql c параметром skip-grant-tables
* затем поменять пароль от root в базе на тот, который указан в /root/.my.cnf
* перезапустить mysql без skip-grant-tables