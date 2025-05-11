

Здравствуйте!

По предоставленной документации не нашел варианта установки в случаях если n8n развернут через Docker контейнеры. В данном случае вы можете попробовать установить библиотеку через Dockerfile. Содержимое Dockerfile примерно должно быть такое и он должен быть размещен в директории `/opt/beget/n8n`:
```
# Dockerfile.n8n-sharp

FROM docker.n8n.io/n8nio/n8n:1.82.3

USER root

RUN apt-get update && apt-get install -y \
    libvips-dev \
    && rm -rf /var/lib/apt/lists/*

RUN npm install sharp

USER node

```
Так же в файл **docker-compose.yml** нужно будет добавить между строками 87 и 88 содержимое:
```
build:
  context: .
  dockerfile: Dockerfile.n8n-sharp
```
Выглядеть с учетом строк будет примерно так:
```
 86   n8n:
 87     <<: *shared
 88     build:
 89       context: .
 90       dockerfile: Dockerfile.n8n-sharp
 91     labels:
```

После чего пере собрать контейнеры:
```
cd /opt/beget/n8n
docker-compose down
docker-compose build n8n
docker-compose up -d
```
Эта последовательность является примерной. Более подробно как установить библиотеку для проектов внутри докер контейнера рекомендую уточнить у разработчиков библиотеки.
