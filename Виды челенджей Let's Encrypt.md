
---

# Проверка HTTP-01

Этот тип проверки используется чаще всего. Let’s Encrypt выдаёт ACME-клиенту токен, а ACME-клиент записывает этот токен в файл на web-сервере по пути `http://YOUR_DOMAIN/.well-known/acme-challenge/TOKEN`. В этом файле содержится сам токен, плюс отпечаток ключа вашего аккаунта. Как только ACME-клиент сообщит Let’s Encrypt, что файл готов, Let’s Encrypt будет пытаться получить этот файл по URL (возможно, несколько раз, с различных адресов). Если полученный ответ будет верным, проверка считается успешной, и сертификат для выбранного домена готов к использованию. Если проверка прошла неудачно, требуется повторить попытку с новым сертификатом.

Реализация проверки HTTP-01 разрешает редиректы запросов, общим числом не более 10. Редиректы принимаются только на адреса, начинающиеся с “http:” или “https:”, и только на порты 80 или 443. Редиректы на IP-адреса не принимаются. Если редирект выполнялся на URL c HTTPS, то сертификат не считается подтверждённым (т.к. проверка для новых сертификатов, может обнаружить самоподписанные или просроченные сертификаты).

Проверка HTTP-01 выполняется только с использованием порта 80. Произвольный порт для проверки снижает надёжность, и потому запрещён стандартом ACME.

Плюсы:

- Упрощённая автоматизация процесса, не требующая дополнительных знаний по настройке доменов
- Выпуск сертификатов для доменов хостинг-провайдеров, привязанных по CNAME.
- Совместим с уже настроенными веб-серверами.

Минусы:

- Не работает, если ваш провайдер блокирует порт 80 (это редко, но это делают некоторые провайдеры).
- Не подходит для сертификатов с подстановкой (wildcard-сертификатов).
- Для каждого web-сервера требуется свой файл.

# Проверка DNS-01

Эта проверка требует подтверждения прав на домен с помощью специальной TXT-записи для доменного имени. Это сложнее в настройке, чем для проверки HTTP-01, но покрывает сценарии использования, недоступные для проверки HTTP-01. Кроме того, проверка DNS-01 разрешает выпускать сертификаты с подстановкой (wildcard-сертификаты). После того, как Let’s Encrypt передаёт ACME-клиенту токен, клиент создаёт содержимое TXT-записи на основе токена и ключа аккаунта, и записывает её в `_acme-challenge.YOUR_DOMAIN`. Далее, Let’s Encrypt запрашивает TXT-запись в DNS-зоне домена. Если значения совпадают - сертификат готов к использованию!

Крайне важно автоматизировать процессы выпуска и отзыва сертификатов, поэтому проверку DNS-01 имеет смысл использовать, когда DNS-провайдер предоставляет API для автоматических обновлений. Наше сообщество поддерживает [список таких DNS-провайдеров](https://community.letsencrypt.org/t/dns-providers-who-easily-integrate-with-lets-encrypt-dns-validation/86438). DNS-провайдер может быть либо регистратором доменов (компанией, у которой вы купили доменное имя), либо сторонней организацией. Если вы захотите сменить DNS-провайдера, нужно будет сделать незначительные изменения у регистратора доменов. Ожидать окончания срока действия сертификатов при этом не требуется.

Обратите внимание, что размещение полноправной учётной записи DNS API на вашем веб-сервере значительно увеличивает вероятность взлома этого веб-сервера. Лучше использовать [учётную запись с ограниченными возможностями](https://www.eff.org/deeplinks/2018/02/technical-deep-dive-securing-automation-acme-dns-challenge-validation), или выполнять проверку DNS-01 на отдельном сервере, с последующим копированием сертификатов на web-сервер.

Let’s Encrypt придерживается стандартов DNS для поиска TXT-записи при проверке DNS-01, поэтому можно задействовать записи CNAME или NS для делегирования права ответа за другие DNS-зоны. Например [настроив субдомен `_acme-challenge`](https://www.eff.org/deeplinks/2018/02/technical-deep-dive-securing-automation-acme-dns-challenge-validation) для специального сервера валидации. Или, если DNS-провайдер медленно обновляется, и вы хотите использовать более производительный сервер.

Для большинства DNS-провайдеров характерен “период обновления данных”, показывающий, сколько времени пройдёт с момента изменения DNS-записи до обновления всех DNS-серверов провайдера. Этот период сложно оценить, особенно когда DNS-провайдеры применяют [anycast](https://en.wikipedia.org/wiki/Anycast). В этом случае, в зависимости от геолокации, вы и Let’s Encrypt будете взаимодействовать с разными серверами, и получать различные результаты. Лучшие DNS API дают вам возможность автоматически проверять полноту распространения обновления. Если DNS-провайдер такой информации не даёт, придётся настроить ACME-клиент на длительное (не менее часа) ожидание перед запуском валидации.

Для одного и того же доменного имени допускается несколько TXT-записей. Например, когда требуется одновременно выполнить проверку и для обычного сертификата, и для wildcard-сертификата. Удаляйте неактуальные TXT-записи, т.к. из-за большого размера ответа сервера при проверке Let’s Encrypt признает проверку неудачной.

Плюсы:

- Подходит для сертификатов с подстановкой (wildcard-сертификатов).
- Работает в случае нескольких web-серверов.

Минусы:

- Хранение учётной записи для API на web-сервере достаточно рискованно.
- У Вашего провайдера DNS может не быть API для управления.
- У DNS API может не быть методов оценки период обновления записей.

# TLS-SNI-01

Проверка была описана в черновиках протокола ACME. Согласно ей, вначале выполняется TLS handshake на порте 443, и посылается специальный [SNI](https://en.wikipedia.org/wiki/Server_Name_Indication) заголовок для поиска сертификата, содержащего токен. Проверка TLS-SNI-01 [отключена в марте 2019](https://community.letsencrypt.org/t/march-13-2019-end-of-life-for-all-tls-sni-01-validation-support/74209) по причине недостаточной безопасности.

# TLS-ALPN-01

Разработана после признания проверки TLS-SNI-01 устаревшей, как [отдельный стандарт](https://tools.ietf.org/html/rfc8737). Как и TLS-SNI-01, проверка TLS-ALPN-01 выполняется через TLS handshake на порте 443. Но в данном случае применяется специальный протокол ALPN, чтобы на запросы валидации отвечали только серверы, знающие о таком типе проверки. Кроме того, становится возможным использовать SNI-поле, совпадающее с именем домена, для которого проводится валидация, для увеличения надёжности проверки.

Эта проверка не подходит для массового использования. Скорее, она предназначена для владельцев TLS-концевых обратных прокси-серверов, для выполнения проверки типа HTTP-01, но внутри слоя TLS, для разделения ответственности. Как правило, это относится к большим хостинг-провайдерам, но распространённые web-серверы типа Apache и Nginx, однажды, поддержат протокол ALPN (а [Caddy уже поддерживает](https://caddy.community/t/caddy-supports-the-acme-tls-alpn-challenge/4860)).

Плюсы:

- Будет работать, даже если порт 80 закрыт.
- Может быть выполнена исключительно на слое TLS.

Минусы:

- Пока не поддерживается Apache, Nginx или Certbot, и, возможно, не будет поддерживаться.
- - Как и для проверки HTTP-01, настраивать нужно ответ от каждого web-сервера.
- Этот метод не может быть использован для проверки доменов по шаблонам.