
---

ФМ подрубается к VPS для получения информации с ip **5.101.156.161**.

Подключение происходит через SSH по ключу, который прокидывается при включении ФМ в настройках ПУ. Используется ключ **rsa-ssh**

Починить signature algorithm ssh-rsa not in PubkeyAcceptedAlgorithms - чинится добавлением в конфиг `PubkeyAcceptedKeyTypes=+ssh-rsa`

