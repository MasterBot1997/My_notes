
Воспроизведение проблемы:
```
apt update && apt install -y nodejs npm
npm install @aws-sdk/client-s3 @aws-sdk/lib-storage @aws-sdk/s3-request-presigner - или другой набор модулей, иногда и на установке по одному модулю зависало
```

Он зависает примерно в таком виде и не изменяется:
```
root@tqfsbwkrcc:~# npm install @aws-sdk/client-s3 @aws-sdk/lib-storage @aws-sdk/s3-request-presigner
(###############⠂⠂⠂) ⠇ reify:@smithy/core: http fetch GET 200 https://registry.npmjs.org/@smithy/core/-/core-3.5.1.tgz 1656ms (cache miss)

```

Делаем пару `Ctrl+C` и процесс падает в D-state:
```
root        1156  0.2  0.5  14984 10496 ?        Ss   16:01   0:00  \_ sshd: root@pts/0
root        1273  0.0  0.3  10412  6784 pts/0    Ss   16:01   0:00  |   \_ -bash
root        7545  4.6  6.1 970016 124728 pts/0   Dl+  16:03   0:12  |       \_ npm install @aws-sdk/client-s3 @aws-sdk/lib-storage @aws-sdk/s3-request-presigner
```
Помогает, что бы остановить, помогает перезагрузка, просто убить процесс не помогает