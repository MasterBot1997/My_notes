
---

- https://serverspace.ru/support/help/kak-sbrosit-root-parol-dlya-mysql-ili-mariadb-v-ubuntu-20-04/?utm_source=google.com&utm_medium=organic&utm_campaign=google.com&utm_referrer=google.com

---

Чтобы установить новый пароль для пользователя root сервиса управления базами данных **(ВНИМАНИЕ** – это НЕ системный пользователь root!), выполните следующие действия:

Войдите на свой VPS сервер под управлением Ubuntu как привилегированный пользователь;

Остановите службу;
```
service mysql stop # для MySQL
service mariadb stop # для MariaDB
```

Временно разрешите аутентификацию без пароля и запустите службу:
```
cat << EOF >> /etc/mysql/my.cnf
[mysqld]
skip-grant-tables
EOF
service mysql start
```

Авторизуйтесь в системе управления базами данных и установите новый root-пароль:
```
mysql
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED BY 'новый_пароль';
exit
```

Удалите директиву _skip-grant-tables_ из «системного» конфигурационного файла и перезапустите службу:
```
service mysql stop; sed -i -e '$d' /etc/mysql/my.cnf && sed -i -e '$d' /etc/mysql/my.cnf; service mysql start
```

**Обратите внимание** – я не случайно запустил команду _sed -i -e ‘$d’_ дважды, а потому, что из конфигурационного файла нужно удалить две последние строки.


Чтобы проверить все ли у вас получилось, давайте попробуем авторизоваться в сервисе:
```
`mysql -uroot -p<новый_пароль>`
```

