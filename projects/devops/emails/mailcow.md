Разворачивание почтового сервера с Mailcow — это крутой выбор, он довольно удобен и интегрирует всё нужное (Postfix, Dovecot, веб-интерфейс и т.д.) в Docker-контейнерах.   

---

### Требования
1. **Сервер**: VPS или dedicated server с минимум 2 GB RAM (лучше 4 GB для комфорта), 20 GB SSD (для начала хватит, но почта растёт быстро).
2. **ОС**: Debian 11 или 12 (стабильные и хорошо поддерживаются Mailcow).
3. **Домен**: У тебя должен быть домен (например, example.com), с доступом к DNS-записям.
4. **Статический IP**: Чистый, не в чёрных списках (проверь через MX Toolbox).
5. **SSH-доступ**: Для управления сервером.

---

### Шаг 1: Подготовка сервера (Debian)
Установим базовую систему и защитим её.
#### 1.1 Установка Debian
- Установи Debian через ISO (если dedicated) или выбери образ у провайдера VPS (Hetzner, DigitalOcean и т.д.).
- Обнови систему:  

```bash
sudo apt update && sudo apt upgrade -y
```
#### 1.2 Базовая безопасность сервера
- **Пользователь**: Не работай от root. Создай нового пользователя:

```bash
adduser username
usermod -aG sudo username
```
- Выйди из root и зайди под username.
- **SSH**: Настрой безопасный доступ:  
    - Открой /etc/ssh/sshd_config:

```bash
sudo nano /etc/ssh/sshd_config
```
- Измени:  
    - Port 2222 (или другой нестандартный порт вместо 22).
      
    - PermitRootLogin no (запрет root-доступа).
      
    - PasswordAuthentication no (только ключи).
  
- Сгенерируй SSH-ключ на своём компьютере:

```bash
ssh-keygen -t rsa -b 4096
ssh-copy-id username@server_ip -p 2222
```
Перезапусти SSH:
```bash
sudo systemctl restart sshd
```
**Фаервол**: Установи UFW и разреши только нужное:
```bash
sudo apt install ufw
sudo ufw allow 2222/tcp  # Твой SSH-порт
sudo ufw allow 80/tcp   # HTTP для Let’s Encrypt
sudo ufw allow 443/tcp  # HTTPS
sudo ufw enable
```
**Fail2ban**: Защита от брутфорса:
```bash
sudo apt install fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```
Настрой jails для SSH в /etc/fail2ban/jail.local:
```ini
[sshd]
enabled = true
port = 2222
maxretry = 5
bantime = 3600
```
#### 1.3 Установка зависимостей
Mailcow работает через Docker, так что ставим его:
```bash
sudo apt install -y docker.io docker-compose
sudo systemctl enable docker
sudo systemctl start docker
```
### Шаг 2: Разворачивание Mailcow
Теперь ставим сам Mailcow.
#### 2.1 Скачивание и настройка
- Клонируем репозиторий:

```bash
cd /opt
sudo git clone https://github.com/mailcow/mailcow-dockerized
cd mailcow-dockerized
```
Генерируем конфигурацию:
```bash
sudo ./generate_config.sh
```
- Укажи:  
    - MAILCOW_HOSTNAME: например, mail.example.com.
      
    - Часовой пояс (например, Europe/Moscow).
      

#### 2.2 Настройка DNS
Для работы почты нужны правильные записи:

- **A**: mail.example.com → IP твоего сервера.
  
- **MX**: example.com → mail.example.com (приоритет 10).
  
- **SPF**: v=spf1 ip4:YOUR_SERVER_IP -all.
  
- **DKIM**: Mailcow сам сгенерирует ключ позже, добавишь в DNS.
  
- **DMARC**: v=DMARC1; p=none; rua=mailto:dmarc@yourdomain.com; (для начала "none", потом ужесточить до "quarantine").
  

#### 2.3 Запуск Mailcow

  

- Подними контейнеры:
```bash
sudo docker-compose up -d
```
Проверь, что всё работает:
```bash
sudo docker-compose ps
```
- Получи DKIM-ключ из /opt/mailcow-dockerized/data/conf/dkim и добавь его в DNS как TXT-запись.
  

#### 2.4 SSL через Let’s Encrypt

  

Mailcow сам настраивает SSL:

  

- Открой mailcow.conf:

```bash
sudo nano mailcow.conf
```
- Убедись, что:  
    - HTTP_BIND=0.0.0.0
      
    - HTTPS_PORT=443
      
    
  
- Перезапусти:

```bash
sudo docker-compose down && sudo docker-compose up -d
```
- SSL активируется автоматически, если DNS настроен.
  

#### 2.5 Первый вход

  

- Открой https://mail.example.com в браузере.
  
- Логин: admin, пароль: сгенерирован в mailcow.conf (поменяй его сразу!).
  

---

### Шаг 3: Безопасность Mailcow

  

Mailcow уже довольно безопасен, но вот что можно усилить:

  

#### 3.1 Сервер

  

- **Обновления**: Регулярно обновляй Debian и Docker:

```bash
sudo apt update && sudo apt upgrade -y
cd /opt/mailcow-dockerized && sudo ./update.sh
```
- **Резервные копии**: Настрой бэкапы:  
    - Конфиги: /opt/mailcow-dockerized/mailcow.conf.
      
    - Данные: /opt/mailcow-dockerized/data.
      
    - Используй rsync или облако (S3, Google Drive).
      
    
  
- **Мониторинг**: Установи htop и iotop для слежения за нагрузкой.
  

#### 3.2 Mailcow

  

- **Пароли**: Смени все дефолтные пароли в админке.
  
- **2FA**: Включи двухфакторку для админского доступа (настройки в веб-интерфейсе).
  
- **Антиспам**: Настрой SpamAssassin и ClamAV в Mailcow (по умолчанию включены, но проверь правила).
  
- **IP-черные списки**: Проверь серверный IP через mxtoolbox.com/blacklists.aspx. Если попал в бан, запроси разблокировку у провайдера.
  
- **Fail2ban для почты**: Добавь защиту портов Mailcow (25, 587, 465, 993, 995):

```ini
[postfix]
enabled = true
port = 25,587
maxretry = 5
```
#### 3.3 Сеть
- **HTTPS only**: Убедись, что HTTP_PORT в mailcow.conf перенаправляет на 443.
  
- **Фаервол**: Открой порты для почты:

```bash
sudo ufw allow 25/tcp   # SMTP
sudo ufw allow 587/tcp  # Submission
sudo ufw allow 465/tcp  # SMTPS
sudo ufw allow 143/tcp  # IMAP
sudo ufw allow 993/tcp  # IMAPS
sudo ufw allow 110/tcp  # POP3
sudo ufw allow 995/tcp  # POP3S
```
### Итог

6. **Установка**: Debian → Docker → Mailcow → DNS → SSL.
  
7. **Безопасность сервера**: SSH с ключами, UFW, Fail2ban, обновления.
  
8. **Безопасность Mailcow**: 2FA, антиспам, резервные копии, защита портов.