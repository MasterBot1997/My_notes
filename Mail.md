Если жалоба на https://hp.beget.ru/ticket/2564025 `### Мы не нашли почтовый сервер (A-запись) на хосте **hbflvixcnr.local**.`
```
root@tdszrlcuoh:~# sed -n 323,325p /etc/exim4/exim4.conf.template
        remote_smtp:
                driver = smtp
                helo_data = $sender_address_domain
```

---

- https://hp.beget.ru/ticket/2369929
