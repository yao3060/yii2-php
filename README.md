# yii2-php

## Extensions

- pdo pdo_mysql opcache zip bcmath redis
- redis
- sockets
- amqp
- mcrypt (7.0 only)
- gd
- intl

## php7.0
```
docker build --tag yao3060/yii2-php:7.0-fpm-alpine  -f ./php70/Dockerfile .
docker push yao3060/yii2-php:7.0-fpm-alpine

```
## php7.4
```
docker build --tag yao3060/yii2-php:7.4-fpm-alpine  -f ./php74/Dockerfile .
docker push yao3060/yii2-php:7.4-fpm-alpine

```