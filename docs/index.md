## Первоначальная настройка и защита сервера Linux
---

### 1. Установка и настройка сервера

#### 1.1. Подключение к серверу
Для начала подключитесь к серверу через SSH:
```bash
ssh username@server_ip_address
```

#### 1.2. Обновление системы
Обновите все установленные пакеты до последних версий:
```bash
sudo apt update
sudo apt upgrade -y
```

### 2. Настройка пользователя

#### 2.1. Создание нового пользователя
Создайте нового пользователя для выполнения задач без использования root-прав:
```bash
sudo adduser newuser
```

#### 2.2. Добавление пользователя в sudoers
Добавьте нового пользователя в группу `sudo`, чтобы он мог выполнять административные команды:
```bash
sudo usermod -aG sudo newuser
```

### 3. Настройка SSH

#### 3.1. Изменение порта SSH
Для повышения безопасности измените стандартный порт SSH (22) на другой:
- Откройте файл конфигурации SSH:
```bash
sudo nano /etc/ssh/sshd_config
```
- Найдите строку `#Port 22` и измените её, убрав комментарий:
```plaintext
Port 2222
```
- Перезапустите SSH:
```bash
sudo systemctl restart ssh
```

#### 3.2. Отключение входа по паролю
Настройте вход только по ключам SSH, отключив вход по паролю:
- Создайте пару ключей на локальной машине:
```bash
ssh-keygen -t rsa -b 4096
```
- Скопируйте публичный ключ на сервер:
```bash
ssh-copy-id newuser@server_ip_address
```
- Откройте файл конфигурации SSH:
```bash
sudo nano /etc/ssh/sshd_config
```
- Найдите и измените/добавьте следующие строки:
```plaintext
PasswordAuthentication no
```
- Перезапустите SSH:
```bash
sudo systemctl restart ssh
```

### 4. Базовые настройки безопасности

#### 4.1. Установка и настройка Firewall
Установите `ufw` (Uncomplicated Firewall) и настройте его:
- Установите `ufw`:
```bash
sudo apt install ufw
```
- Разрешите необходимый порт SSH:
```bash
sudo ufw allow 2222/tcp
```
- Разрешите другие необходимые порты (например, для веб-сервера):
```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

- Включите `ufw`:
```bash
sudo ufw enable
```

#### 4.2. Установка Fail2ban
Установите и настройте Fail2ban для защиты от брутфорс-атак:
- Установите Fail2ban:
```bash
sudo apt install fail2ban
```

- Создайте локальный конфигурационный файл:
```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

- Откройте и отредактируйте файл `/etc/fail2ban/jail.local`:
```bash
sudo nano /etc/fail2ban/jail.local
```

Найдите и измените секцию `[sshd]`:
```plaintext
[sshd]
enabled = true
port = 2222
logpath = /var/log/auth.log
maxretry = 5
```
    
- Перезапустите Fail2ban:
```bash
sudo systemctl restart fail2ban
```

### 5. Дополнительные меры безопасности

#### 5.1. Настройка автоматического обновления
Настройте автоматическое обновление пакетов безопасности:
- Установите пакет `unattended-upgrades`:
```bash
sudo apt install unattended-upgrades
```
- Настройте его:
```bash
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

#### 5.2. Отключение root-входа по SSH
Дополнительно улучшите безопасность, отключив возможность входа под root:

- Откройте файл конфигурации SSH:
```bash
sudo nano /etc/ssh/sshd_config
```

- Найдите и измените строку:
```plaintext
PermitRootLogin no
```

- Перезапустите SSH:
```bash
    sudo systemctl restart ssh
 *ge2.md*FastAPI** - это современный веб-фреймворк для создания веб-приложений с автоматически сгенерированной документацией и встроенной поддержкой валидации данных. **SQLAlchemy** - это библиотека для работы с базами данных в Python, позволяющая создавать асинхронные приложения для взаимодействия с базами данных.
```

### 6. Журналирование и мониторинг

#### 6.1. Установка и настройка системы логирования
Используйте инструменты для централизованного сбора и анализа логов, такие как `rsyslog` или `logrotate`.

- Установите `rsyslog` (если не установлен):
```bash
sudo apt install rsyslog
```

- Убедитесь, что `rsyslog` включен и работает:
```bash
sudo systemctl enable rsyslog
sudo systemctl start rsyslog
```

- Настройте `logrotate` для управления размером логов и их ротацией:
```bash
sudo nano /etc/logrotate.conf
```

#### 6.2. Настройка мониторинга системы
Установите инструменты для мониторинга производительности и безопасности сервера, такие как `netdata`, `Zabbix`, или `Prometheus`.

### 7. Установка и настройка антивирусного ПО

#### 7.1. Установка ClamAV
Установите антивирус ClamAV для регулярной проверки системы на наличие вредоносного ПО.
- Установите ClamAV:
```bash
sudo apt install clamav clamav-daemon
```

- Обновите базы данных вирусов:
```bash
sudo freshclam
```

- Запустите ClamAV и настройте регулярные проверки:
```bash
sudo systemctl start clamav-freshclam
sudo systemctl enable clamav-freshclam
```

### 8. Ограничение сетевых подключений

#### 8.1. Ограничение доступа к критическим сервисам
Используйте файлы конфигурации `hosts.allow` и `hosts.deny` для ограничения доступа к критическим сервисам.

- Откройте и настройте `/etc/hosts.allow`:
```bash
sudo nano /etc/hosts.allow
```

Пример:
```plaintext
sshd: 192.168.1.0/24
```

- Откройте и настройте `/etc/hosts.deny`:
```bash
sudo nano /etc/hosts.deny
```

Пример:
```plaintext
sshd: ALL
```

### 9. Обнаружение руткитов

#### 9.1. Установка и использование chkrootkit
Установите `chkrootkit` для проверки системы на наличие руткитов.

- Установите `chkrootkit`:
 ```bash
sudo apt install chkrootkit
```

- Запустите проверку:
```bash
sudo chkrootkit
```

### 10. Политики управления учетными записями и паролями

#### 10.1. Настройка сложности паролей
Используйте `PAM` для настройки политики сложности паролей.
1. Откройте файл `/etc/pam.d/common-password`:
```bash
sudo nano /etc/pam.d/common-password
```

- Добавьте или измените строку для настройки сложности паролей:
```plaintext
password requisite pam_pwquality.so retry=3 minlen=12 difok=3
```

#### 10.2. Отключение неиспользуемых учетных записей
Проверьте и отключите неиспользуемые учетные записи:
```bash
sudo usermod -L unuseduser
```

### 11. Обновление ядра и модулей

#### 11.1. Установка и обновление ядра
Убедитесь, что у вас установлена последняя версия ядра:
```bash
sudo apt install linux-image-generic
sudo apt install linux-headers-generic
```

#### 11.2. Перезагрузка системы
Перезагрузите систему для применения обновлений:
```bash
sudo reboot
```

### 12. Защита сетевых подключений

#### 12.1. Установка и настройка WireGuard
WireGuard — это современный VPN, который легко настроить и использовать.

- Установите WireGuard:
```bash
sudo apt install wireguard
```

- Создайте ключи для сервера:
```bash
umask 077
wg genkey | tee privatekey | wg pubkey > publickey
```

- Настройте конфигурацию сервера WireGuard:
```bash
sudo nano /etc/wireguard/wg0.conf
```

- Пример конфигурационного файла:
```plaintext
[Interface]
PrivateKey = <private_key>
Address = 10.0.0.1/24
ListenPort = 51820

[Peer]
PublicKey = <client_public_key>
AllowedIPs = 10.0.0.2/32
```

- Включите и запустите WireGuard:
 ```bash
sudo wg-quick up wg0
sudo systemctl enable wg-quick@wg0
```

#### 12.2. Установка и настройка Shadowsocks
Shadowsocks — это прокси-сервер, который помогает обходить цензуру.

- Установите Shadowsocks:
```bash
sudo apt install shadowsocks-libev
```

- Настройте конфигурацию Shadowsocks:
 ```bash
sudo nano /etc/shadowsocks-libev/config.json
```

- Пример конфигурационного файла:
```json
{
    "server": "0.0.0.0",
    "server_port": 8388,
    "local_port": 1080,
    "password": "your_password",
    "timeout": 300,
    "method": "aes-256-gcm"
}
```

- Запустите и включите Shadowsocks:
```bash
sudo systemctl start shadowsocks-libev
sudo systemctl enable shadowsocks-libev
```

#### 12.3. Установка и настройка Outline
Outline — это набор инструментов, разработанный Google для создания и управления прокси-серверами.

- Установите Docker:
```bash
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

- Установите Outline Manager:
```bash
sudo docker run -d -p 80:80 -p 443:443 -v ~/outline-server:/root/outline-server --name outline-server-1 ghcr.io/jigsawcode/outline-server:latest
```

- Следуйте инструкциям, чтобы настроить и запустить Outline Manager.

