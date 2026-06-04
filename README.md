# Безопасность

Сгенерировать случайный набор символов с помощью команды:

    openssl rand -base64 32

Обновление списка пакетов

    sudo apt update -y
    sudo apt upgrade -y

# Настройка сайта

Устанавливаем NGINX, Certbot, модуль Certbot для автоматической настройки SSL в NGINX
   
    apt install nginx certbot python3-certbot-nginx -y

После установки можно выпустить SSL-сертификат командой:

    certbot --nginx -d domen.com
Нужно будет ввести email

Открыть документ

    nano /etc/nginx/sites-available/default

Заменить 

    listen [::]:443 ssl ipv6only=on; # managed by Certbot
    listen 443 ssl; # managed by Certbot

На

    listen 127.0.0.1:8443 ssl;

Применить

    nginx -t

Перезагрузить

    systemctl restart nginx

Удалить заглушку nginx и добавить сайт

    rm /var/www/html/index.nginx-debian.html
    sudo nano /var/www/html/index.html

Применить, перезагрузить

    nginx -t
    systemctl restart nginx


Установка wget и curl

    sudo apt install wget curl -y

Создаём подключение 

<img width="1366" height="258" alt="image" src="https://github.com/user-attachments/assets/d6bd2218-2a5b-4d93-8fe1-aa957e8fd97a" />

Можно включить Sockopt и TCP Fast Open

Заменить транспорт

<img width="1366" height="123" alt="image" src="https://github.com/user-attachments/assets/27932b02-91e4-40cd-a21b-2f41171b7e14" />






# Настройка WARP
<details>
<summary>Показать/скрыть</summary>
# Создать cloudflare gpg key
    curl -fsSL https://pkg.cloudflareclient.com/pubkey.gpg | sudo gpg --yes --dearmor --output /usr/share/keyrings/cloudflare-warp-archive-keyring.gpg

# Add this repo to your apt repositories
    echo "deb [signed-by=/usr/share/keyrings/cloudflare-warp-archive-keyring.gpg] https://pkg.cloudflareclient.com/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/cloudflare-client.list

# Install
    sudo apt-get update && sudo apt-get install cloudflare-warp

# Settings
    warp-cli registration new

   warp-cli mode proxy 

   warp-cli connect
</details>
