# Безопасность
<details>
<summary>Общие</summary>

Диспетчер задач 

    htop
   
Обновление списка пакетов

    sudo apt update -y
    sudo apt upgrade -y

Вход под root:

    su root (с паролем root)
    sudo -i (с парлем user)

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

Создаём подключение VLESS Reality под свой сайт

<img width="1366" height="392" alt="image" src="https://github.com/user-attachments/assets/a74b5613-1eb3-42db-9993-b6da6c69d6e6" />

Можно включить Sockopt и TCP Fast Open

<img width="1366" height="391" alt="image" src="https://github.com/user-attachments/assets/b063c39a-49e5-46d5-94c2-2bed77fc1fb3" />

Заменить транспорт на XHTTP

<img width="1366" height="212" alt="image" src="https://github.com/user-attachments/assets/98cc2c08-c474-4088-91a8-675dbba257b7" />
<img width="1366" height="391" alt="image" src="https://github.com/user-attachments/assets/d9db4950-e8a3-464d-ac45-c1a1471eca17" />


# bbr

Проверить работу на сервере, вводим:
        
    sysctl net.ipv4.tcp_congestion_control

Работает - bbr нет - cubik

Включить

    x-ui
    26

# Подписка

Включить подписку

Порт подписки 20196 или любой другой, который не занят (делаем больше случайных настроек в настройках панели)

Корневой путь URL-адреса подписки меняем с sub на любой другой, например tv65  /tv/

Путь к файлу открытого ключа сертификата подписки /root/cert/p.kirnetwiz.top/fullchain.pem

Путь к файлу закрытого ключа сертификата подписки /root/cert/p.kirnetwiz.top/privkey.pem

# Telegram-бот
<details>
<summary>Показать/скрыть</summary>

Создаём бота
@BotFather

ПолучАем id
@userinfobot

![image](https://github.com/user-attachments/assets/612dc1e8-c923-411b-9745-40f5f389794f)
</details>

# DNS-over-LESS 
<details>
<summary>Показать/скрыть</summary>
    
    tcp://8.8.8.8
    tcp://1.1.1.1
    https://dns.google/dns-query
    https://cloudflare-dns.com/dns-query

![image](https://github.com/user-attachments/assets/a030b9da-7d78-44a4-b54d-ac86e64ab740)
</details>

# Port hopping
<details>
<summary>Показать/скрыть</summary>
1 Открыть диапозон адресов

    sudo ufw allow 30000:30100/tcp
    sudo ufw allow 30000:30100/udp

2 Проверить что xRay работает на порту 443

3 Настройка Port Hopping через iptables. 

    sudo iptables -t nat -A PREROUTING -p tcp --dport 30000:30100 -j REDIRECT --to-port 443
    sudo iptables -t nat -A PREROUTING -p udp --dport 30000:30100 -j REDIRECT --to-port 443

4 Сохранение правил iptables

    sudo apt install -y iptables-persistent
    sudo netfilter-persistent save
    sudo netfilter-persistent reload

5 Проверка применения правил

    sudo iptables -t nat -L -v -n

6 Убедитесь, что порт 443 слушается:

    sudo lsof -i -P -n | grep LISTEN
</details>

# Настройка WARP

<details>
<summary>Показать/скрыть</summary>

Создать cloudflare gpg key

    curl -fsSL https://pkg.cloudflareclient.com/pubkey.gpg | sudo gpg --yes --dearmor --output /usr/share/keyrings/cloudflare-warp-archive-keyring.gpg

Add this repo to your apt repositories

    echo "deb [signed-by=/usr/share/keyrings/cloudflare-warp-archive-keyring.gpg] https://pkg.cloudflareclient.com/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/cloudflare-client.list

Install

    sudo apt-get update && sudo apt-get install cloudflare-warp

Settings

    warp-cli registration new

    warp-cli mode proxy 

    warp-cli connect

</details>


# Бэкап и восстановление: Настройки всегда под защитой. 
<details>
<summary>Показать/скрыть</summary>

Самый простой способ сделать бэкап базы данных это из панели управления, т.е. с вебки. 
3x-ui умеет 2 бэкапа, первый это конфигурация самой панели и настроек соединений, а второй это файл базы данных о пользователях и их настроек. 

Экспорт настроек и базы данных. - Заходим на главную страницу панели 3x-ui - Справа находим “Конфигурация” и “Бэкап и восстановление” - Сохраняем конфигурацию в файл и делаем экспорт базы данных. 

Выбрать пункт 12. Должны получить такую надпись: 
[x-ui and xray stopped successfully 

Обычно файл конфигурации хранится по этому пути: 
/usr/local/x-ui/bin/config.json 
А файл базы данных хранится по этому пути: 
/etc/x-ui/x-ui.db

Выбрать пункт 11. Должны получить такую надпись: 
x-ui Started Successfully

</details>

# Тесты
<details>
<summary>Показать/скрыть</summary>

# Speedtest c сервера

    wget -qO- speedtest.artydev.ru | bash

# Speedtest до конкретного сервера

    bash <(curl -s https://raw.githubusercontent.com/kir460/3x/main/sped.sh)

# IP.Check.Place: Проверка репутации IP-адреса. Предварительно можно понять "чистоту" IP и пройтись по публичным спискам об IP.

    bash <(curl -Ls IP.Check.Place) -l en

My Traceroute (MTR): Утилита для диагностики сети. Утилита позволяет отследить маршрут пакетов и выявить проблемные узлы.

    mtr ya.ru
    mtr ip-address
</details>

# Поиск оптимального сервера в подсетях TLS 1.3 через TLS Scanner на Windows
<details>
<summary>Показать/скрыть</summary>
1) Установите Python: 
Скачайте последнюю версию Python с официального сайта: Python.org. 
Во время установки обязательно отметьте опцию Add Python to PATH.

2) Скачиваем последнюю версию TLS Scanner https://github.com/XTLS/RealiTLScanner

3) Скачайте файл и создайте папку по пути C:\RealiTLScanner и перенесите в неё RealiTLScanner-windows-64.exe
4) Запустите PowerShell
5) Вводим команду и переходим в папку: cd C:\RealiTLScanner
6) Введите в окне PowerShell строку ниже, вместо 255.255.255.255 подставьте свой IP адрес сервера, где будет 3x-ui:

    .\RealiTLScanner-windows-64.exe -addr 255.25.255.255 -port 443 -thread 30 > results.txt
   
8) Аргумент > results.txt складывает в текстовый файл в папку, откуда мы возьмем результаты. Спустя 2-5 мин можем  прекращать поиск и перейти в тестовый файл results.txt
9) Меняем SNI на нужный, будьте внимательны к пунктуации. Старайтесь использовать более редкие варианты, и стараться избегать более популярных. 
</details>
