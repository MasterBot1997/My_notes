
Ссылка на доку - https://kb.fastpanel.direct/cli/intro

---

# Описание

FASTPANEL предоставляет интерфейс командной строки (CLI), реализованный через `mogwai`утилиту ( `/usr/local/bin/mogwai`). Утилита будет работать только при запуске как `root`.

Чтобы просмотреть список доступных команд, используйте:

```
mogwai --help
```

Чтобы узнать больше о конкретной команде, используйте:

```
mogwai command --help
```

Чтобы увидеть список команд с описаниями, используйте:

```
mogwai --help-long
```

---

# Веб-сайты

## Список сайтов на

**Команда**

```
mogwai sites list
```

**Пример вывода**

```
ID      SERVER_NAME             ALIASES                         OWNER                   MODE    PHP_VERSION     IPS             DOCUMENT_ROOT1       example.com             www.example.com                 example_com_usr         mpm_itk 82              127.0.0.1   /var/www/example_com_usr/data/www/example.com
```

**Примечание** : Сайты `IDs`из этого списка используются в других командах.

## Создание нового

**Команда**

```
mogwai sites create --server-name=SERVER-NAME --owner=OWNER [<flags>]
```

**Параметры**

- `--owner`: Пользователь в системе FASTPANEL, которому будет принадлежать сайт.
- `--server-name=SERVER-NAME`: Доменное имя для сайта.
- `-a`, `--alias=ALIAS`: Псевдонимы для сайта, такие как www поддомены. Можно указать несколько псевдонимов.
- `--ip=IP`: IP-адрес сервера, на котором будет работать сайт. Можно указать несколько IP-адресов.
- `-m`, `--php-mode=PHP-MODE`: Режим обработки PHP, может быть `cgi`, `mpm_itk`, `php_fpm`, `fcgi`.
- `--php-version=PHP-VERSION`: Версия PHP. Можно указать только установленную версию, без точки - например, 73.
- `--create-user`: Создайте пользователя для сайта.

**Пример команды**

```
mogwai sites create --server-name=example.com --owner=user --create-user --alias=www.example.com --ip=127.0.0.1 --php-mode=fcgi --php-version=73
```

## Удаление

**Команда**

```
mogwai sites delete --id=ID
```

Сайт `ID`можно получить у `mogwai sites list`команды.

**Пример команды**

```
mogwai sites delete --id=987
```

Эта команда удалит сайт с идентификатором 987.

## Изменение

**Команда**

```
mogwai sites update --id=ID [<flags>]
```

Сайт `ID`можно получить у `mogwai sites list`команды.

**Параметры**

- `-i`, `--id=ID`: Идентификатор сайта.
- `-a`, `--add-alias=ADD-ALIAS`: Добавить псевдоним, можно указать несколько псевдонимов.
- `--del-alias=DEL-ALIAS`: Удалить псевдоним. Можно указать несколько псевдонимов.
- `--add-ip=ADD-IP`: Добавьте IP-адрес из настроек сайта, можно указать несколько IP-адресов.
- `--del-ip=DEL-IP`: Удалить IP-адрес из настроек сайта, можно указать несколько IP-адресов.
- `-m`, `--php-mode=PHP-MODE`: Режим обработки PHP, может быть `cgi`, `mpm_itk`, `php_fpm`, `fcgi`.
- `--php-version=PHP-VERSION`: Версия PHP. Можно указать только установленную версию, без точки - например, 73.
- `--gzip`: Включить сжатие.
- `--no-gzip`: Отключить сжатие.
- `--gzip-comp-level=LEVEL`: Установите уровень сжатия, 1-9.
- `--expires=EXPIRES`: Установите время кэширования статического контента.
- `--worker-count=WORKER-COUNT`: Установить количество рабочих процессов для PHP-FPM

Вы также можете обновить настройки для нескольких сайтов на сервере с помощью команды

```
mogwai sites batch-update
```

- Чтобы изменить настройки для всех сайтов, используйте `-a`флаг.
- Чтобы изменить настройки для выбранных сайтов, укажите несколько `--id`флагов с их `IDs`.

**Пример команды**

```
mogwai sites update --id=3 -a www1.example.com -m cgi --php-version=56
```

Эта команда добавит псевдоним www1.example.com, изменит обработчик на cgi и версию PHP на 5.6 для сайта с идентификатором 3.

**Пример команды**

```
mogwai sites batch-update -m cgi --php-version=56 --id 3 --id 2
```

Эта команда изменит обработчик PHP для сайтов с ID 2 и 3.

---

# Базы данных

## Получение списка

**Команда**

```
mogwai databases servers list
```

**Пример вывода**

```
ID      NAME                    TYPE            LOCAL   HOST    USERNAME        AVAIL1       mysql(localhost)        mysql           true            fastuser        true2       postgresql(localhost)   postgresql      true            fastuser        true
```

Сервер `IDs`используется в других командах.

## Получение списка

**Команда**

```
mogwai databases list
```

**Пример вывода**

```
ID      NAME            SERVER_NAME             LOCAL           HOST    CHARSET OWNER   SIZE    CREATE_AT                              1       db1             mysql(localhost)        localhost       true    utf8mb4 user    0       2024-05-28 10:03:03.227413266 +0000 UTC2       database43s     mysql(localhost)        localhost       true    utf8mb4 user82  0       2024-05-28 10:07:15.001640584 +0000 UTC
```

## Синхронизация

Если вы недавно добавили или удалили базы данных, и они еще не появились в FASTPANEL, вы можете принудительно синхронизировать списки

**Команда**

```
mogwai databases sync
```

## Создание новой

**Команда**

```
mogwai databases create [flags]
```

**Параметры**

- `-n`, `--name=NAME`: Имя базы данных.
- `-o`, `--owner="fastuser"`: Пользователь FASTPANEL, которому будет добавлена ​​база данных.
- `-s`, `--site=SITE`: Веб-сайт в FASTPANEL, к которому будет добавлена ​​база данных.
- `-c`, `--charset="utf8mb4"`: Кодировка базы данных.
- `-u`, `--username=USERNAME`: Имя пользователя базы данных.
- `-p`, `--password=PASSWORD`: Пароль пользователя базы данных.

**Пример команды**

```
mogwai databases create --server=1 -n db1 -o fastuser -s example.com -u dbuser -p dbpassword1
```

Эта команда создаст базу данных с именем db1 на сервере баз данных по умолчанию (--server=1) и «привяжет» ее к сайту example.com.

---

# Пользователи

## Создание нового

**Команда**

```
mogwai users create --username=USERNAME --password=PASSWORD [--role="USER"]
```

**Параметры**

- `-u`, `--username=USERNAME`: Укажите имя нового пользователя.
- `-p`, `--password=PASSWORD`: Укажите пароль для нового пользователя.
- `-r`, `--role="USER"`: Укажите роль для нового пользователя. Поддерживаемые роли: RESELLER и USER. Роль по умолчанию: USER.

**Информация об использовании**

Чтобы создать нового пользователя с именем пользователя `testuser`, паролем `My$ecretPassword123`и ролью `RESELLER`, используйте следующую команду:

```
mogwai users create --username=testuser --password=My$ecretPassword123 --role="RESELLER"
```

**Дополнительные примечания**

- Имена пользователей должны быть уникальными и не могут содержать пробелов или точек.
- Пароли должны быть длиной не менее 8 символов. Мы рекомендуем использовать по крайней мере одну заглавную букву, одну строчную букву, одну цифру и один специальный символ.

---

# Электронная почта

## Получение списка

**Команда**

```
mogwai emails domains list
```

**Пример вывода**

```
ID      NAME            FALLBACK        DKIM    ENABLED OWNER_ID        OWNER           CREATE_AT1       example.com                     true    true    1               example_com_usr 2024-01-04 15:46:02+03:00
```

Домены `IDs`используются в других командах.

## Добавление

**Команда**

```
mogwai emails domains create
```

**Параметры**

- `--domain=DOMAIN`: Доменное имя электронной почты.
- `-o`, `--owner="fastuser"`: Пользователь FASTPANEL, которому будет добавлен домен электронной почты.

**Пример создания домена электронной почты example1.com под пользователем fastuser**

```
mogwai emails domains create --domain=example1.com -o fastuser
```

## Получение списка

**Команда**

```
mogwai emails boxes list
```

**Параметры**

- `--domain=DOMAIN`: Имя почтового домена

**Пример команды**

```
mogwai emails boxes list --domain example.com
```

**Пример вывода**

```
ID      ADDRESS                 ALIASES REDIRECTS       SIZE    QUOTA   ENABLED OWNER_ID        OWNER           CREATE_AT1       user@example.com                                0       0       true    4               example_com_usr 2024-02-08 11:13:01+03:00
```

Вывод `ID`команды from используется в других командах.

## Создание

**Команда**

```
mogwai emails boxes create
```

**Параметры**

- `--domain=DOMAIN`: Доменное имя электронной почты.
- `-l`, `--login=LOGIN`: Имя почтового ящика без домена.
- `-p`, `--password=PASSWORD`: Пароль к почтовому ящику.

**Пример команды**

```
mogwai emails boxes create  --domain=example.com --login=user --password=MySecretPassword123
```

Эта команда создаст почтовый ящик с именем `user@example.com`и паролем `MySecretPassword123`.

## Удаление

**Команда**

```
mogwai emails boxes delete
```

**Параметры**

- `-b`, `--box=BOX`: Идентификатор почтового ящика.

**Пример команды**

```
mogwai emails boxes delete -b 1
```

Эта команда удалит почтовый ящик с идентификатором 1.

## Импорт почтовых ящиков и

**Команда**

```
mogwai emails boxes import[sync] [<flags>]
```

**Параметры**

- `-i`, `--import_config=IMPORT_CONFIG`: Путь к файлу списка импорта
- `--force`: Только для `import`режима — очистить существующие почтовые ящики.

Для импорта почты необходимо подготовить список почтовых ящиков на сервере в формате:

```
IMAP_HOST;SOURCE_ADDR;SOURCE_PASSWORD;DEST_ADDR
```

**Параметры**

- `IMAP_HOST`: Адрес почтового сервера IMAP.
- `SOURCE_ADDR`: Почтовый ящик на исходном сервере.
- `SOURCE_PASSWORD`: Пароль для подключения IMAP к почтовому ящику на исходном сервере. Некоторые службы, такие как Google, требуют создания пароля приложения для доступа IMAP [https://support.google.com/accounts/answer/185833?hl=en](https://support.google.com/accounts/answer/185833?hl=en)
- `DEST_ADDR`: Почтовый ящик на сервере FASTPANEL, на который передаются сообщения.

**Пример содержимого файла**

```
imap.gmail.com;test@gmail.comu;password;test@example.comimap.gmail.com;test1@gmail.com;password;test1@example.com
```

Доступны два режима:

**Режим импорта**

```
mogwai emails boxes import
```

В этом режиме все сообщения копируются полностью, почтовые ящики на сервере FASTPANEL не должны быть созданы или пусты - при выполнении команды FASTPANEL создает необходимые почтовые ящики. Если почтовые ящики с содержимым уже существуют, будет выведена ошибка. Можно использовать `--force`флаг - тогда почтовый ящик будет очищен.

**Пример запуска команды со списком почтовых ящиков, сохраненным в файле /root/import.txt, и использованием ключа --force**

```
mogwai emails boxes import --import_config=/root/import.txt --force
```

**Режим синхронизации**

```
mogwai emails boxes sync
```

В этом режиме Панель управления загружает сообщения с исходного сервера, ничего не удаляя. Его следует использовать, если в исходном почтовом ящике после первого импорта появились новые сообщения, которые также необходимо перенести.

**Пример команды**

```
mogwai emails boxes sync --import_config=/root/import.txt
```


