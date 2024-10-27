#web #certbot
Плагин [certbot-nginx](https://archlinux.org/packages/?name=certbot-nginx) предоставляет автоматическую настройку [Nginx (Русский)](https://wiki.archlinux.org/title/Nginx_(%D0%A0%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%B9) "Nginx (Русский)"). Он пытается найти конфигурацию каждого домена, а также добавляет рекомендованные для безопасности параметры, настройки использования сертификатов и пути к сертификатам Certbot. 

### Первоначальная настройка:
**Устанавливаем**
```shell
apt install certbot python3-certbot-nginx
```
**Создаем сертификаты**
```shell
certbot --nginx
```
> Для примера будет использоваться доменное имя *example.com*

**Вывод:**
```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Enter email address (used for urgent renewal and security notices)
 (Enter 'c' to cancel): administrator@example.com
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```
> Вводим почту
```
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.4-April-3-2024.pdf. You must agree in
order to register with the ACME server. Do you agree?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: y
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```
> Соглашаемся с условиями обслуживания (что бы не проходить этот пункт можно застить создание сертификатов с ключем `--agree-tos` )
```
Would you be willing, once your first certificate is successfully issued, to
share your email address with the Electronic Frontier Foundation, a founding
partner of the Let's Encrypt project and the non-profit organization that
develops Certbot? We'd like to send you email about our work encrypting the web,
EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: n
Account registered.
```
> Certbot предложит вам поделиться своим адресом электронной почты с Electronic Frontier Foundation (EFF), вы можете ответить, набрав Y для «Да» или N для «Нет» в зависимости от ваших предпочтений. Для  пропуска можно использовать ключи `--eff-email` и `--no-eff-email`
```
Which names would you like to activate HTTPS for?
We recommend selecting either all domains, or all domains in a VirtualHost/server block.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: example.com
2: www.example.com
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel):
```
> Предлагает выбрать для каких именно доменных имен создавать сертификаты. По умолчанию - для всех предложенных.

**Если всё прошло успешно**
```
Requesting a certificate for example.com and www.example.com

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/example.com/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/example.com/privkey.pem
This certificate expires on 2024-09-22.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

Deploying certificate
Successfully deployed certificate for example.com to /etc/nginx/sites-enabled/example.com
Successfully deployed certificate for www.example.com to /etc/nginx/sites-enabled/example.com
Congratulations! You have successfully enabled HTTPS on https://example.com and https://example.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```
### Обновить сертификаты:
Для обновления сертификатов сервер должен быть доступен по 80 порту из мира (отключаем все геопы). Как вариант проверить доступность по HTTP можно на сайте https://check-host.net
```shell
certbot renew
```
### Изменить сертификаты без изменения файлов конфигурации nginx:
```shell
certbot --nginx certonly
```
### Отладка
##### Иммитация получения сертификатов (для отладки)
```shell
certbot renew --dry-run
```
##### .well-known/acme-challenge/ 


Создайте тестовый файл:
```shell
sudo mkdir -p /var/www/html/.well-known/acme-challenge/
echo "test" | sudo tee /var/www/html/.well-known/acme-challenge/test
```
**Проверьте права на директории и файлы**: Убедитесь, что Nginx имеет доступ к директории и файлам. Задайте правильные права доступа:
```shell
sudo chown -R www-data:www-data /var/www/html/.well-known/
sudo chmod -R 755 /var/www/html/.well-known/
```
Убедитесь, что в конфигурации Nginx настроен блок для обработки запросов в директорию `.well-known/acme-challenge/`. Проверьте, что запросы к файлам этой директории обрабатываются правильно.

Пример блока в конфигурации:
```shell
server {
    listen 80;
    server_name mssg.fesukr.com.ua www.mssg.fesukr.com.ua;

    location /.well-known/acme-challenge/ {
        alias /var/www/html/.well-known/acme-challenge/;
        try_files $uri =404;
    }
}

```
После внесения изменений перезапустите Nginx:
```shell
sudo systemctl restart nginx
```


Проверьте доступ к этому файлу через браузер или `curl`:
```shell
curl http://mssg.fesukr.com.ua/.well-known/acme-challenge/test
```

##### Проверяем днс запись
```shell
dig A mssg.fesukr.com.ua
dig A www.mssg.fesukr.com.ua`
```
Вы должны получить ответ с IP-адресом вашего сервера, например:
```shell
mssg.fesukr.com.ua. 300 IN A 199.189.49.52  
www.mssg.fesukr.com.ua. 300 IN A  199.189.49.52
```