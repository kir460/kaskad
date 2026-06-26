# Безопасность
<details>
<summary>Общие</summary>

Диспетчер задач 

    htop
   
Обновление списка пакетов

    sudo apt update -y
    sudo apt upgrade -y

Вход под root:
    su root(с паролем root), sudo -i (с парлем user)

</details>

<details>
<summary>Поменять hostname</summary>

    sudo nano /etc/hostname
    sudo nano /etc/hosts
    hostnamectl
    sudo hostnamectl set-hostname myserver
    hostname
    sudo reboot

</details>

#  Поменять пароль и ssh ключ

Сгенерировать случайный набор символов с помощью команды:

    openssl rand -base64 32

Поменять пароль
    
    sudo passwd root

Создание нового пользователя

    adduser x

Добавление пользователя в группу sudo (опционально, для предоставления админ-доступа)

    usermod -aG sudo x

Переключение на нового пользователя

    su x

Создание директории для хранения SSH-ключей

    mkdir -p ~/.ssh

Открытие файла для добавления публичного ключа

    nano ~/.ssh/authorized_keys

Вставьте сюда содержимое публичного ключа из PuTTYgen и сохраните файл

Установка корректных прав доступа к директории и файлу ключей

    chmod 700 ~/.ssh
    chmod 600 ~/.ssh/authorized_keys

Переключение обратно на root (если необходимо)

    exit

Открытие конфигурационного файла SSH для редактирования
   
    sudo nano /etc/ssh/sshd_config

Заменить порт

Port 22102

Найдите строку "PermitRootLogin yes" и замените её на "PermitRootLogin no", 

При необходимости измените "PasswordAuthentication yes" на "PasswordAuthentication no"

Перезапуск службы SSH для применения изменений

    sudo systemctl restart ssh
    sudo reboot

# Установка UFW

    sudo apt update
    sudo apt install ufw

Открыть порты

    sudo ufw allow 22102/tcp #ssh
    sudo ufw allow 443/tcp #https
    sudo ufw allow 8443/tcp #https
    sudo ufw allow 80/tcp #http
    sudo ufw allow 20196/tcp #subscribe
    sudo ufw allow *****/tcp #pannel

Блокировка ICMP-запросов для предотвращения двустороннего пинга

    sudo nano /etc/ufw/before.rules
    
    # ok icmp codes for INPUT
    -A ufw-before-input -p icmp --icmp-type destination-unreachable -j DROP
    -A ufw-before-input -p icmp --icmp-type time-exceeded -j DROP
    -A ufw-before-input -p icmp --icmp-type parameter-problem -j DROP
    -A ufw-before-input -p icmp --icmp-type echo-request -j DROP
    -A ufw-before-input -p icmp --icmp-type source-quench -j DROP

    # ok icmp code for FORWARD
    -A ufw-before-forward -p icmp --icmp-type destination-unreachable -j DROP
    -A ufw-before-forward -p icmp --icmp-type time-exceeded -j DROP
    -A ufw-before-forward -p icmp --icmp-type parameter-problem -j DROP
    -A ufw-before-forward -p icmp --icmp-type echo-request -j DROP

Перезапустить

    sudo ufw disable && ufw enable

Включить, выключить, перезагрузить firewall

    sudo ufw enable
    sudo ufw disable
    sudo ufw reload

Проверить статус UFW

    sudo ufw status verbose

Чтобы увидеть все правила UFW:

    sudo ufw show added

Чтобы убедиться, что UFW автоматически запускается при старте системы:

    sudo systemctl is-enabled ufw
    
Чтобы включить автозапуск UFW:

    sudo systemctl enable ufw 

Закрыть, удалить старый порт

    sudo ufw deny 22/tcp 
    sudo ufw delete allow 22102/tcp
    sudo ufw delete 1

# Настройка сайта
<details>
<summary>Показать\скрыть</summary>
    
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

Удалить заглушку nginx и добавить сайт

    rm /var/www/html/index.nginx-debian.html
    sudo nano /var/www/html/index.html

Применить, перезагрузить

    nginx -t
    systemctl restart nginx

Установка wget и curl

    sudo apt install wget curl -y
</details>

# Устоновка 3x-ui

Репозиторий разработчика: https://github.com/MHSanaei/3x-ui

    bash <(curl -Ls https://raw.githubusercontent.com/mhsanaei/3x-ui/master/install.sh)

Выбираем базу данных
<img width="669" height="164" alt="image" src="https://github.com/user-attachments/assets/441ca161-24b9-4f4d-bbd1-eb3a1b023f67" />

Panel Port settings? random


Создаём подключение 

<img width="1366" height="258" alt="image" src="https://github.com/user-attachments/assets/d6bd2218-2a5b-4d93-8fe1-aa957e8fd97a" />
<img width="1366" height="645" alt="image" src="https://github.com/user-attachments/assets/90dc67c5-2ea2-4310-87c3-5ee0ddd602d1" />

Можно включить Sockopt и TCP Fast Open

<img width="1366" height="391" alt="image" src="https://github.com/user-attachments/assets/b063c39a-49e5-46d5-94c2-2bed77fc1fb3" />

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
