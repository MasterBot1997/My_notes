
1. yii vps/show-customer-vps - показывает информацию о текущих VPS серверах

- -a -  Показывает информацию о севрерах которые создавались за какое-то время аккаунта(вероятно за все время существования) - Показывать удалённые vps.
- -r -  Показывать установленные ограничения.

---

2. yii vps/show-system-info - отображает инфу о системе:

- Output of ip a - сетевые настройки
- Output of ip r - Отображене таблицы маршрутизации. 
- Output of netstat -tulpan command. We check internal sockets and listen - Отображает какие порты прослушиваются на VPS
- Output of cat /etc/passwd command - Пользователи и их информация в системе
- Output of cat /root/.ssh/authorized_keys command - Добавленные ключи SSH
- Output of cat /etc/ssh/sshd_config command - Конфигурация SSH клиента
- Output of tail -100 /var/log/auth.log command - Последние 100 строк лога авторизаций в  системе вкл удачные и неудачные
- Output of fail2ban-client status sshd command - отображает сколько было неудачных попыток авторизации за все время пока лог не был удален, так же показывает текущее кол-во блокировок и какие ip были заблоканы
- Output of iptables -t filter -S command - Команда `iptables -t filter -S` используется для отображения всех текущих правил в таблице `filter` в человекочитаемом виде. Таблица `filter` — это основная таблица в `iptables`, которая используется для фильтрации пакетов, то есть для разрешения или запрета прохождения сетевых пакетов через систему.
- Output of free -m command - Команада для отображения статистики задействованной RAM включая SWAP(Если подключено)
- Output of df -h command - Отображенаяет информации об использовании пространства на файловых системах. Опция `-h` (или `--human-readable`) предоставляет данные в удобном для чтения формате, содержающем единицы измерения, такие как KB, MB, и GB.
- Output of top -b -n1 command - Отображает инфу о процессах и производительности системы, ключи нужны для отображения на момент запуска команды в выводе
- Output of dmesg -l err command(all error in dmesg) - Отображает информацию о системных ошибках
- Output of last 100 lines of dmesg - Отображает информацию о системных событиях, таких как загрузка драйверов, ошибки оборудования и другие системные сообщения

---

3. yii cloud/show-customer-mysql - Показывает мнфу только по облачным БД, ключ -a отображает инфу о удаленных БД

---

4. yii vps/show-user-backup - отображает БЭкапы VPS пользователя вкл удаленных VPS. Ключ -f отображает только файловые бэкапы, Ключ -r отображает информацию только о текщих VPS, скрывает инфу о удаленных