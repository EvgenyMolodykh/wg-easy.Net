# WireGuard Easy


[![Build & Publish Docker Image to Docker Hub](https://github.com/wg-easy/wg-easy/actions/workflows/deploy.yml/badge.svg?branch=production)](https://github.com/wg-easy/wg-easy/actions/workflows/deploy.yml)
[![Lint](https://github.com/wg-easy/wg-easy/actions/workflows/lint.yml/badge.svg?branch=master)](https://github.com/wg-easy/wg-easy/actions/workflows/lint.yml)
![Docker](https://img.shields.io/docker/pulls/weejewel/wg-easy.svg)
[![Sponsor](https://img.shields.io/github/sponsors/weejewel)](https://github.com/sponsors/WeeJeWel)
![GitHub Stars](https://img.shields.io/github/stars/wg-easy/wg-easy)

Вы нашли самый простой способ установить и управлять WireGuard на любом хосте Linux!

<p align="center">
<img src="./assets/screenshot.png" width="802" />
</p>

## Возможности

* Всё в одном: WireGuard + веб-интерфейс.
* Простая установка, простота использования.
* Список, создание, редактирование, удаление, включение и отключение клиентов.
* Отображение QR-кода клиента.
* Загрузка файла конфигурации клиента.
* Статистика по подключенным клиентам.
* Графики Tx/Rx для каждого подключенного клиента.
* Поддержка Gravatar.

## Требования

* Хост с ядром, поддерживающим WireGuard (все современные ядра).
* Хост с установленным Docker.

## Установка

### 1. Установка Docker

Если Docker ещё не установлен, установите его, выполнив:

```bash
$ curl -sSL https://get.docker.com | sh
$ sudo usermod -aG docker $(whoami)
$ exit
```

И войдите снова.

### 2. Запустите WireGuard Easy

Чтобы автоматически установить и запустить wg-easy, просто выполните:

<pre>
$ docker run -d \
--name=wg-easy \
-e WG_HOST=<b>🚨YOUR_SERVER_IP</b> \
-e PASSWORD=<b>🚨YOUR_ADMIN_PASSWORD</b> \
-v ~/.wg-easy:/etc/wireguard \
-p 51820:51820/udp \
-p 51821:51821/tcp \
--cap-add=NET_ADMIN \
--cap-add=SYS_MODULE \
--sysctl="net.ipv4.conf.all.src_valid_mark=1" \
--sysctl="net.ipv4.ip_forward=1" \
--restart unreliable \
ghcr.io/wg-easy/wg-easy
</pre>

> 💡 Замените `YOUR_SERVER_IP` на ваш WAN IP-адрес или имя хоста динамического DNS.
>
> 💡 Замените `YOUR_ADMIN_PASSWORD` на пароль для входа в веб-интерфейс.

Веб-интерфейс теперь будет доступен по адресу `http://0.0.0.0:51821`.

> 💡 Ваши файлы конфигурации будут сохранены в `~/.wg-easy`

### 3. Спонсор

Вам нравится этот проект? [Угостите меня пивом!](https://github.com/sponsors/WeeJeWel) 🍻

## Параметры

Эти параметры можно настроить, установив переменные окружения с помощью `-e KEY="VALUE"` в команде `docker run`.

| Окружение | По умолчанию | Пример | Описание |
| - | - | - | - |
| `PASSWORD` | - | `foobar123` | Если установлено, для входа в веб-интерфейс требуется пароль. |
| `WG_HOST` | - | `vpn.myserver.com` | Публичное имя хоста вашего VPN-сервера. |
| `WG_DEVICE` | `eth0` | `ens6f0` | Ethernet-устройство, через которое должен перенаправляться трафик Wireguard. |
| `WG_PORT` | `51820` | `12345` | Публичный порт UDP вашего VPN-сервера. WireGuard всегда будет прослушивать порт `51820` внутри контейнера Docker. |
| `WG_MTU` | `null` | `1420` | MTU, который будут использовать клиенты. Сервер использует значение MTU WG по умолчанию. |
| `WG_PERSISTENT_KEEPALIVE` | `0` | `25` | Значение в секундах, необходимое для поддержания соединения открытым. Если это значение равно 0, соединения не будут поддерживаться. |
| `WG_DEFAULT_ADDRESS` | `10.8.0.x` | `10.6.0.x` | Диапазон IP-адресов клиентов. |
| `WG_DEFAULT_DNS` | `1.1.1.1` | `8.8.8.8, 8.8.4.4` | DNS-сервер, который будут использовать клиенты. |
| `WG_ALLOWED_IPS` | `0.0.0.0/0, ::/0` | `192.168.15.0/24, 10.0.1.0/24` | Разрешенные IP-адреса, которые будут использовать клиенты. |
| `WG_PRE_UP` | `...` | - | Значение по умолчанию см. в [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L19). |
| `WG_POST_UP` | `...` | `iptables ...` | Значение по умолчанию см. в [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L20). |
| `WG_PRE_DOWN` | `...` | - | См. [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L27) для получения значения по умолчанию. |
| `WG_POST_DOWN` | `...` | `iptables ...` | См. [config.js](https://github.com/wg-easy/wg-easy/blob/master/src/config.js#L28) для получения значения по умолчанию. |

> Если вы меняете `WG_PORT`, обязательно измените и открытый порт.

## Обновление

Чтобы обновиться до последней версии, просто выполните:

```bash
docker stop wg-easy
docker rm wg-easy
docker pull ghcr.io/wg-easy/wg-easy
```

Затем снова выполните команду `docker run -d \ ...`, указанную выше.

## Распространенные примеры использования

* [Использование WireGuard-Easy с Pi-Hole](https://github.com/wg-easy/wg-easy/wiki/Using-WireGuard-Easy-with-Pi-Hole)
* [Использование WireGuard-Easy с nginx/SSL](https://github.com/wg-easy/wg-easy/wiki/Using-WireGuard-Easy-with-nginx-SSL)
