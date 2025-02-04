
---

### Полезные ссылки

- https://www.dmosk.ru/instruktions.php?object=fail2ban#lists
- 

---

### Команды

- Посмотреть текущие блокировку и информацию о неудачных попытках авторизации `fail2ban-client status sshd` или `fail2ban-client status <jail>`
- Разбанить ip `fail2ban-client set <имя правила> unbanip <IP-адрес>`

**witelist**:
Для того, чтобы задать общую настроку, откроем наш файл default:

vi /etc/fail2ban/jail.d/default.conf

... и добавим:

```
[DEFAULT]  
...  
ignoreip = 192.168.0.0/24 95.95.95.95
```

- в данном примере под фильтры не будут попадать адреса с **192.168.0.1** по **192.168.0.255** и адрес **95.95.95.95**._

Для конкретного правила настройки будут, примерно, следующие:
```
vi /etc/fail2ban/jail.d/ssh.conf

[ssh]  
...  
ignoreip = 192.168.1.22
```

- в данном примере мы добавили в белый список один адрес **192.168.1.22**, который не будет блокироваться._

Обязательно перезагружаемся, чтобы настройки применились:
```
systemctl restart fail2ban
```

_* добавление адреса в белый список не удаляет его из блокировки. Поэтому, если IP попал в блок, нужно будет его [удалить вручную](https://www.dmosk.ru/instruktions.php?object=fail2ban#delete)._