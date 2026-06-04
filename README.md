Сгенерировать случайный набор символов с помощью команды:

    openssl rand -base64 32

# Настройка сайта

Обновляем список пакетов, устанавливаем NGINX, Certbot, модуль Certbot для автоматической настройки SSL в NGINX
   
    apt update && apt install nginx certbot python3-certbot-nginx -y

После установки можно выпустить SSL-сертификат командой:

    certbot --nginx -d domen.com

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
Добавить сайт

     rm /var/www/html/index.nginx-debian.html
     sudo nano /var/www/html/index.html
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





















# Скрипт для настройки каскадных соединений, переадресации трафика (NAT) и ускорения сети на Linux

<details>
<summary>Показать/скрыть</summary>
Идеальное решение от автора канала anten-ka для создания "мостов" к VPN (AmneziaWG, WireGuard), Proxy (VLESS, XRay) и мессенджерам (MTProto/TProxy).

---

## 🌟 Возможности

* **🚀 Быстрый запуск:** После первой установки скрипт доступен из любой папки по команде `gokaskad`.
* **⚡ High Speed Core:** Работает через **Iptables (Kernel NAT)**. Никаких лишних процессов, скорость ограничена только каналом сервера.
* **🏎 BBR Turbo:** Автоматически включает алгоритм **Google BBR** для максимального ускорения TCP соединений.
* **🔀 Мультипротокольность:**
    * Поддержка **UDP** (AmneziaWG, WireGuard).
    * Поддержка **TCP** (VLESS, VMess, Reality).
    * Поддержка **MTProto / TProxy** (Telegram).
* **🛠 Кастомные правила:** Возможность указывать разные порты для входа и выхода (проброс SSH, RDP и нестандартных сервисов).
* **🌐 Мульти-туннелирование:** Создавайте 2, 5, 10 соединений на разных портах одновременно.
* **🧠 Умная настройка:**
    * Автоматическое открытие портов в UFW.
    * Умное удаление правил `FORWARD` (файрвол остается чистым даже при разных портах).
    * Сохранение правил после перезагрузки (`netfilter-persistent`).
* **📱 Удобное меню:** Просмотр списка правил, удаление по одному, полный сброс настроек.

---

## 🚀 Быстрая установка

Подключитесь к вашему VPS (Ubuntu/Debian) под пользователем `root` и выполните одну команду:

```bash
wget -O install.sh https://raw.githubusercontent.com/kir460/kaskad/main/install.sh && chmod +x install.sh && ./install.sh
```

### ⚡ Последующие запуски
При первой установке скрипт автоматически прописывается в систему. В дальнейшем, чтобы открыть меню, просто введите в терминале:
```bash
gokaskad
```

---

## 📚 Инструкция: Как настроить каскад

Каскад позволяет вам подключаться к локальному серверу, который будет незаметно пересылать весь трафик на ваш зарубежный сервер.

### Шаг 1: Подготовка
У вас должны быть на руках данные от вашего **зарубежного** сервера:
* **IP-адрес** (например: `45.10.20.30`)
* **Порт** (на котором работает целевой сервис)

### Шаг 2: Настройка этого сервера
1. Запустите скрипт командой `gokaskad`.
2. В меню выберите нужный протокол или "Кастомное правило".
3. Введите IP-адрес зарубежного сервера и порт(ы).
4. Скрипт сообщит об успешном создании "моста".

### Шаг 3: Настройка клиента (Важно!)
1. Откройте ваше приложение (AmneziaWG, v2rayNG, Telegram и т.д.).
2. В настройках соединения найдите поле **Endpoint / Адрес сервера**.
3. **Замените** зарубежный IP-адрес на **IP-адрес этого сервера**.
4. Сохраните настройки и подключитесь. Готово!
<details/>
