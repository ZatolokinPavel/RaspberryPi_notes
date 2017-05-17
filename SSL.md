# Настройка SSL
Настройка SSL для Nginx на Debian 8 (jessie) с использованием сертификатов Let's Encrypt  
Источники:  
https://certbot.eff.org/#debianjessie-nginx  
https://habrahabr.ru/post/318952/

### Установка Certbot
Описано тут: https://certbot.eff.org/#debianjessie-nginx  
Сначала включаем репозиторий Jessie backports repo добавив строчку  
`deb http://ftp.debian.org/debian jessie-backports main`  
в файл **sources.list** (or add a new file with the ".list" extension to /etc/apt/sources.list.d/)  
и обновив список пакетов `apt-get update`

Теперь можно установить сам Certbot  
`$ sudo apt-get install certbot -t jessie-backports`

### Настройка
Запишем основные настройки в файл конфигурации, который certbot ожидает найти в `/etc/letsencrypt/cli.ini:`
```
authenticator = webroot
webroot-path = /var/www/html
post-hook = service nginx reload
text = True
```

### Подготовка Nginx
Certbot будет создавать необходимые для проверки прав на домен файлы в подкаталогах ниже по иерархии к указанному. Вроде таких: `/var/www/html/.well-known/acme-challenge/example.html`. Эти файлы должны будут быть доступны из сети на целевом домене по крайней мере по HTTP: `http://www.example.com/.well-known/acme-challenge/example.html`. Поэтому в общем случае для получения сертификата необходимо во всех блоках server добавить следующий блок до других блоков location:
```nginx
location /.well-known {
    root /var/www/html;
}
```