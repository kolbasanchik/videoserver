
# Инструкция по созданию своей системы видеонаблюдения на ZoneMinder и NextCloud

## Для установки потребуется:
* Доступ к интернету
* Микрокомпьютер (здесь OrangePI)
* Диск на 500ГБ или больше
* Свободный компьютер
* Промежуточный сервер со статическим IP*
*альтернатива — статический IP на каждой из машин
## Лексика
* apt (Advanced Packaging Tool) — пакетный менеджер для установки программ в Debian и основанных на нем ос
* Подписи — цифровые подписи, для проверки авторства программы
* Репозиторий — место, для хранения данных в интернете



## Установка
Устанавливаем вебсервер, базу данных, а также другие программы, нужные для успешной установки ZoneMinder:
```
apt install apache2 apache2-utils php libapache2-mod-php php-mysql php-gd php-mysql mariadb-server apt-transport-https gnupg wget
```
Выполним установку безопасности mysql (на все вопросы отвечаем да):
```
mysql_secure_installation
```
Добавляем подписи для репозитория zoneminder:
```
wget -O - https://zmrepo.zoneminder.com/debian/archive-keyring.gpg | apt-key add -
```
Добавляем репозиторий zoneminder в локальный список репозиториев:
```
echo "deb https://zmrepo.zoneminder.com/debian/release-1.36 bullseye/" >> /etc/apt/sources.list
```
Обновляем репозитории и устанавливаем ZoneMinder:
```
apt update
apt install zoneminder
```


## Настройка
Устанавливаем разрешения для каталога файлов zoneminder:
```
chmod 740 /etc/zm/zm.conf
chown root:www-data /etc/zm/zm.conf
chown -R www-data:www-data /usr/share/zoneminder/
```

Подключаем к вебсерверу apache нужные модули:
```
a2enmod rewrite
a2enmod cgi
```

Также можно подключить модули expires и headers для улучшения производительности:
```
a2enmod expires
a2enmod headers
```

Подключаем конфигурацию zoneminder в apache:
```
a2enconf zoneminder
```

Запускаем наш видеосервер:
```
systemctl reload apache2
systemctl enable zoneminder
systemctl start zoneminder
```

## Веб-интерфейс
Далее подключаемся в панель управления сервером по адресу: [ip адрес]/zm, чтобы узнать свой локальный ip-адрес, введите:
```
hostname -I
# или
ip addr # тут нужно найти подходящий адаптер, обычно он под номером 2
```

В Options -> System нужно выставить часовой пояс (Europe/Moscow в моем случае).

В Options -> System можно задать новый пароль администратора.

Теперь, когда базовая система ZoneMinder установлена, дальнейшая настройка происходит через веб-интерфейс:
* https://zoneminder.readthedocs.io/en/stable/userguide/options.html
