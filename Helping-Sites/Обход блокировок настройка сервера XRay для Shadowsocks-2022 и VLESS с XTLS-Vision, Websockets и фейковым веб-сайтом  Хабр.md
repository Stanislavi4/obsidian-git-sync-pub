---
created: 2024-02-23T05:55:08 (UTC +06:00)
tags: [xray,v2ray,sing-box,shadowsocks,vmess,vless,nginx,websocket,cdn]
source: https://habr.com/ru/articles/728836/
author: Внесистемный дезинтегратор
---

# Обход блокировок: настройка сервера XRay для Shadowsocks-2022 и VLESS с XTLS-Vision, Websockets и фейковым веб-сайтом / Хабр

> ## Excerpt
> Статья опубликована под лицензией Creative Commons BY-NC-SA.Предыдущие статьи серии:&laquo;Современные технологии обхода блокировок: V2Ray, XRay, XTLS, Hysteria и все‑...

---
_Статья опубликована под лицензией Creative Commons_ [**_BY-NC-SA_**](https://creativecommons.org/licenses/by-nc-sa/4.0/)**_._**

Предыдущие статьи серии:

«[Современные технологии обхода блокировок: V2Ray, XRay, XTLS, Hysteria и все‑все‑все](https://habr.com/ru/articles/727868/)»

«[Программы‑клиенты для протоколов недетектируемого обхода блокировок сайтов: V2Ray/XRay, Clash, Sing‑Box, и другие](https://habr.com/ru/articles/728696/)»

С протоколами разобрались, с клиентами разобрались, теперь наконец‑то настало время рассказать о том, как же настроить свой личный прокси‑сервер с современными протоколами для обхода блокировок. Мы будем настраивать сервер на базе XRay (который является форком известного V2Ray, и еще я немного упомяну Sing‑Box) с протоколами Shadowsocks-2022 и VLESS с транспортом XTLS‑Vision и фейковым веб‑сайтом для защиты от выявления. И в качестве запасного варианта на том же сервере мы настроим fallback на VLESS+Websockets, чтобы была возможность работать через CDN типа Cloudflare, если вдруг IP-адрес вашего сервера попадет под блокировку. В конце я приведу настройки десктопных и мобильных клиентов для подключения ко всему этому.

Поехали.

Настройку буду описывать под Debian или Ubuntu Linux. Если у вас на VPS стоит другой дистрибутив, то там будет примерно все то же самое, хотя некоторые команды и названия пакетов могут отличаться.

Итак, допустим у нас уже есть VPS с Debian или Ubuntu в какой-нибудь заморской юрисдикции, у него есть IP-адрес, на нем настроен SSH и вообще все пока что неплохо. И еще у вас должен быть какой-нибудь домен, не обязательно платный (хотя сейчас по акциям можно зарегистрировать домен в какой-нибудь не очень популярной зоне всего за доллар-два в год), подойдет даже DynDNS. Если чего-то из вышеописанного у вас нет, советую ознакомиться [этой](https://habr.com/ru/articles/688528/) и [этой](https://habr.com/ru/articles/687512/) статьей, там в начале описывается базовая установка и настройка VPS с Linux и регистрация бесплатного домена через DynDNS. Ну а мы идем дальше.

Первый вариант настройки я приведу для "пустого сервера" - это если на вашем сервере нет никаких других сервисов (но потом можно будет добавить еще и веб-сайт, да). Во второй половине статьи я расскажу, как настроить XRay когда у вас на машине уже крутится веб-сервер и вы не хотите лишний раз трогать его конфигурацию. Третий вариант будет с Docker ~для самых маленьких~.

### Вариант первый, полный, подробный

Разработчики XRay подготовили скрипт, который автоматически скачивает XRay под используемую систему и создает systemd-юнит (спасибо [@alegz81](https://habr.com/users/alegz81) что напомнил): [https://github.com/XTLS/Xray-install](https://github.com/XTLS/Xray-install)

Устанавливается одной длинной командой

```
$ bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install
```

Единственное различие от описанного в статье при конфигурации будет в том, что конфиги XRay будут лежать не в /opt/xray, а в /usr/local/etc/xray/.

Либо же можем установить все ручками.

Идем вот сюда: [https://github.com/XTLS/Xray-core/releases](https://github.com/XTLS/Xray-core/releases) и скачиваем самый свежий билд XRay-core:

```
<span>$ wget</span> https://github.com/XTLS/Xray-core/releases/download/v1.8.0/Xray-linux-64.zip
```

Cоздаем директорию, распаковываем и делаем файл исполняемым (он поставляется в .zip-архиве, поэтому разрешения при упаковке-распаковке теряются):

```
<span>$ mkdir</span> /opt/xray<br><span>$ unzip</span> ./Xray-linux-64.zip <span>-d</span> /opt/xray<br><span>$ chmod</span> <span>+</span>x /opt/xray/xray
```

Далее создадим systemd-юнит и вставим туда следущий текст (я использую nano, вы, понятное дело, можете использовать vi и вообще все что угодно):

```
<span>$ nano</span> /usr/lib/systemd/system/xray.service
```

```
[Unit]
Description=XRay

[Service]
Type=simple
Restart=on-failure
RestartSec=30
WorkingDirectory=/opt/xray
ExecStart=/opt/xray/xray run -c /opt/xray/config.json

[Install]
WantedBy=multi-user.target
```

```
<span>$ systemctl</span> daemon-reload<br><span>$ systemctl</span> enable xray
```

Обратите внимание - в данном случае xray запустится от пользователя root. Это не очень хорошо в плане безопасности, я сделал это так в примере для упрощения мануала, но по-хорошему нужно создать для xray отдельного пользователя, запускать его от него, не забыть выставить ему права для чтения на директории и файлы от certbot/letsencrypt (об этом чуть дальше), и чтобы была возможность повесить сервер на порт 443 или другие <1000, [выставить специальную опцию](https://serverfault.com/a/394136) на бинарник/процесс.

На этом установка XRay закончена, дальнейшие действия будут одинаковы и при ручной настройке, и при использовании скрипта.

Нам будут нужны TLS-сертификаты.

Устанавливаем certbot и запрашиваем сертификат для нашего домена (например, example.com):

```
<span>$ apt</span> install certbot<br><span>$ certbot</span> certonly <span>--standalone</span> <span>--preferred</span><span>-challenges</span> http <span>-d</span> example.com
```

Если вам нужно иметь два домена или домен и поддомен (например, один будет доступен напрямую, другой через CDN), то можно указать ещё один аргумент -d в этой команде и у вас будет сертификат сразу для двух доменов. А ещё оно поддерживает wildcards.

Certbot спросит ваш емайл на всякий случай, спросит согласны ли вы с правилами, запросит сертификат от LetsEncrypt, положит его в папочку /etc/letsencrypt и создаст правило, чтобы он обновлялся каждые 3 месяца. При каждом обновлении сертификата нужно перезапускать XRay-сервер, давайте попросим certbot делать это автоматически:

```
<span>$ nano</span> /etc/letsencrypt/renewal/example.com.conf
```

и там в конец добавим строку

```
renew_hook = systemctl reload xray
```

Теперь переходим к самому интересному. Создаем и редактируем конфиг:

```
<span>$ nano</span> /opt/xray/config.json <span># или в /usr/local/etc/xray/ в случае использования скрипта</span>
```

```
{<br>  "log": {<br>    "loglevel": <span>"info"</span><br>  },<br>  "routing": {<br>    "rules": [],<br>    "domainStrategy": <span>"AsIs"</span><br>  },<br>  "inbounds": [<br>    {<br>      "port": <span>23</span>,<br>      "tag": <span>"ss"</span>,<br>      "protocol": <span>"shadowsocks"</span>,<br>      "settings": {<br>        "method": <span>"2022-blake3-aes-128-gcm"</span>,<br>        "password": <span>"aaaaaaaaaaaaaaaabbbbbbbbbbbbbbbb"</span>,<br>        "network": <span>"tcp,udp"</span><br>      }<br>    },<br>    {<br>      "port": <span>443</span>,<br>      "protocol": <span>"vless"</span>,<br>      "tag": <span>"vless_tls"</span>,<br>      "settings": {<br>        "clients": [<br>          {<br>            "id": <span>"7957c33c-d9ca-11ed-afa1-0242ac120002"</span>,<br>            "email": <span>"user1@myserver"</span>,<br>            "flow": <span>"xtls-rprx-vision"</span><br>          }<br>        ],<br>        "decryption": <span>"none"</span>,<br>        "fallbacks": [<br>          {<br>            "path": <span>"/myverysecretpath"</span>,<br>            "dest": <span>"@vless-ws"</span><br>          },<br>          {<br>            "dest": <span>"8080"</span><br>          }<br>        ]<br>      },<br>      "streamSettings": {<br>        "network": <span>"tcp"</span>,<br>        "security": <span>"tls"</span>,<br>        "tlsSettings": {<br>          "alpn": [<br>            <span>"http/1.1"</span>,<br>            <span>"h2"</span><br>          ],<br>          "certificates": [<br>            {<br>              "certificateFile": <span>"/etc/letsencrypt/live/example.com/fullchain.pem"</span>,<br>              "keyFile": <span>"/etc/letsencrypt/live/example.com/privkey.pem"</span><br>            }<br>          ]<br>        }<br>      },<br>      "sniffing": {<br>        "enabled": <span>true</span>,<br>        "destOverride": [<br>          <span>"http"</span>,<br>          <span>"tls"</span><br>        ]<br>      }<br>    },<br>    {<br>      "listen": <span>"@vless-ws"</span>,<br>      "protocol": <span>"vless"</span>,<br>      "tag": <span>"vless_ws"</span>,<br>      "settings": {<br>        "clients": [<br>          {<br>            "id": <span>"7957c33c-d9ca-11ed-afa1-0242ac120002"</span>,<br>            "email": <span>"user2@myserver"</span><br>          }<br>        ],<br>        "decryption": <span>"none"</span><br>      },<br>      "streamSettings": {<br>        "network": <span>"ws"</span>,<br>        "security": <span>"none"</span>,<br>        "wsSettings": {<br>          "path": <span>"/myverysecretpath"</span><br>        }<br>      }<br>    }<br>  ],<br>  "outbounds": [<br>    {<br>      "protocol": <span>"freedom"</span>,<br>      "tag": <span>"direct"</span><br>    },<br>    {<br>      "protocol": <span>"blackhole"</span>,<br>      "tag": <span>"block"</span><br>    }<br>  ]<br>}
```

На что обратить внимание. В inbounds мы задаем правила обработки входящих подключений - первым идет Shadowsocks-2022 на 23 порту (можете использовать любой другой порт, само собой). О том, что эта версия протокола именно 2022 говорит method "2022-blake3-aes-128-gcm". Ключ - любой в шестнадцатеричной форме, его длина зависит от типа шифра, в примере 128-битный шифр, если используете 256-битный, то ключ, соответственно, должен быть в два раза длиннее.

Дальше идет VLESS через TLS, стандартный порт 443. В секции "clients" задаются пользователи-клиенты, в примере он только один. ID клиента можно сгенерировать любым онлайновым UUID-генератором. Также для юзера задается опция "flow" со значением "xtls-rprx-vision", означающая что этот пользователь будет подключаться с использованием XTLS-Vision. В настройках "streamSettings" вы можете увидеть пути к сертификатам, которые мы запросили у LetsEncrypt, в пути должен быть файл соответствующий вашему домену. В "fallbacks" задаются правила о том, что делать, если юзер был не опознан, либо подключение производится не через чистый VLESS-протокол: если мы видим HTTP-запрос с URI /myverysecretpath, то передаем подключение на обработчик vless-ws, для всего остального - на порт 8080, где у нас будет висеть веб-сервер с фейковым (или даже настоящим) веб-сайтом.

И наконец, третим идет вариант VLESS через Websocket, на том же 443 порту. Таким образом, например, можно подключаться к серверу не напрямую, а через CDN, что поможет если ваш сервер вдруг заблокировали цензоры или если вы подключаетесь через строгий корпоративный прокси. Настройка его почти аналогична предыдущему пункту, UUID пользователя там указан тот же самый, единственные различие - нет опции "xtls-rprx-vision", потому что через CDN она работать не будет, и есть секция "wsSettings", где указан тот же секретный путь на сервере /myverysecretpath что и в fallbacks.  
См. также: [Особенности проксирования через CDN/Websocket/gRPC для обхода блокировок](https://habr.com/ru/articles/761798/)

В комментариях к предыдущей статье упоминали, что websocket-транспорт не всегда работает надежно и эффективно, а еще при очень больших объемах передаваемого трафика Cloudflare может обидиться и начать просить перейти на платный тариф. Поэтому вместо websocket некоторые советуют использовать gRPC-транспорт. Я пробовал, и у меня не получилось нормально настроить fallback на gRPC. В [комментарии](https://habr.com/ru/articles/728836/comments/#comment_25445520) к статье хабраюзер [@s7eepz](https://habr.com/users/s7eepz) приложил пример настройки fallback'а на gRPC через Nginx - но важный момент, Nginx должен быть собран с gRPC-модулем. В Debian/Ubuntu и в официальных репозиториях от разработчиков он собран без него.

И как вы могли заметить, в конфиге упомянут порт 8080 для fallback. На нем у нас должен слушать веб-сервер с сайтом для маскировки. Самый просто вариант это сделать - поставить позади него nginx:

```
$ apt install nginx
$ nano /etc/nginx/sites-enabled/default
$ systemctl restart nginx
```

Где /etc/nginx/sites-enabled/default в самом простом случае будет представлять собой что-то типа такого:

```
server {
        listen 127.0.0.1:8080 default_server;
        listen [::1]:8080 default_server;

        root /var/www/html;
        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                try_files $uri $uri/ =404;
        }
}
```

(главное изменение по сравнению с дефолтной конфигурацией - сервер слушает не на всех адресах, а только на localhost, и не на 80, а на 8080 порту).

После этого при попытке подключиться к вашему IP-адресу обычным браузером (то, что могут автоматически делать цензоры, пытаясь выявить прокси-сервера), отвечать будет Nginx, отдавая страницы лежащие в /var/www/html. По умолчанию там лежит заглушка, можно закинуть туда какие-нибудь странички и видео с котятками.

Если лень заморачиваться с поднятием фейкового сайта, есть второй вариант - пусть веб-сервер спрашивает у подключающихся логин и пароль, и отклоняет все введенные варианты:

```
location / {
                auth_basic "Administrator’s Area";
                auth_basic_user_file /etc/htpasswd;
        }
```

Сам файл /etc/htpasswd может вообще даже не существовать - нам не нужно проверять правильность пароля, мы будем отклонять все подряд, делая вид что пароль не подошел. Nginx все равно запустится даже без этого файла.

![В браузере это будет выглядеть вот так](%D0%9E%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20XRay%20%D0%B4%D0%BB%D1%8F%20Shadowsocks-2022%20%D0%B8%20VLESS%20%D1%81%20XTLS-Vision,%20Websockets%20%D0%B8%20%D1%84%D0%B5%D0%B9%D0%BA%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/59b993ec1fe16c3a6a13c44816a16f19.png "В браузере это будет выглядеть вот так")

В браузере это будет выглядеть вот так

Третий вариант - переадресовывать подключения на какой-нибудь другой сайт. XRay не умеет перекидывать подключения на внешние сервера, только на локальные, поэтому тут нам опять поможет Nginx:

```
server {
    listen 127.0.0.1:8080 default_server;
    listen [::1]:8080 default_server;

    server_name _;

    location / {
         proxy_pass http://lib.ru;
    }
}
```

В результате при попытке открытия адреса прокси браузером загрузится зеркало lib.ru - замените его на какой-нибудь другой сайт. Использовать для этого какие-либо популярные или навороченный сайты явно не стоит, а вот какую-нибудь богом забытую хомпагу эпохи Web 1.0 или безымянную webftp-файлосвалку - уже можно. А чтобы некоторые тупые боты или пауки поисковых систем не нагнали вам трафика, можно добавить [опции ratelimit-модуля в Nginx](https://www.nginx.com/blog/rate-limiting-nginx/) и ограничить скорость передачи данных с "переадресованного" сайта, например, до 1 мегабита.

Перезапускаем еще раз xray:

```
$ systemctl restart xray
```

Проверяем что все нормально запустилось:

```
$ journalctl -u xray
```

Например, XRay может ругнуться что не удается распарсить JSON-файл, обычно это связано с лишними запятыми в конце {} блока, в этом случае он укажет, на какой строке ошибка. Исправляем ошибки, перезапускаем еще раз, и переходим к настройке клиентов.

Я буду показывать на примере Nekoray/Nekobox, но абсолютно то же самое можно сделать и в другом клиенте, настройки будут одинаковые. Скачиваем [Nekoray](https://github.com/MatsuriDayo/nekoray/releases), выбираем в настройках core Sing-box (и Nekoray волшебным образом становится Nekobox).

Идем в server -> new profile, и далее заполняем поля следующим образом.

Для прямого VLESS + XTLS-Vision:

![](%D0%9E%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20XRay%20%D0%B4%D0%BB%D1%8F%20Shadowsocks-2022%20%D0%B8%20VLESS%20%D1%81%20XTLS-Vision,%20Websockets%20%D0%B8%20%D1%84%D0%B5%D0%B9%D0%BA%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/4156f608c01de8e22eccf9bc1a0cb2bb.png)

Для VLESS-over-Websockets:

![](%D0%9E%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20XRay%20%D0%B4%D0%BB%D1%8F%20Shadowsocks-2022%20%D0%B8%20VLESS%20%D1%81%20XTLS-Vision,%20Websockets%20%D0%B8%20%D1%84%D0%B5%D0%B9%D0%BA%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/8fe18b4a87948cb821cb150ede204877.png)

Для Shadowsocks:

![](%D0%9E%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20XRay%20%D0%B4%D0%BB%D1%8F%20Shadowsocks-2022%20%D0%B8%20VLESS%20%D1%81%20XTLS-Vision,%20Websockets%20%D0%B8%20%D1%84%D0%B5%D0%B9%D0%BA%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/38b20f307d4c472653bf621172d41e8e.png)

Выбираем нужное подключение в списке на главном окне, кликаем правой кнопкой мыши -> Current Select -> URL test, и видим в логе и в окошке, что пинг успешен:

![](%D0%9E%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20XRay%20%D0%B4%D0%BB%D1%8F%20Shadowsocks-2022%20%D0%B8%20VLESS%20%D1%81%20XTLS-Vision,%20Websockets%20%D0%B8%20%D1%84%D0%B5%D0%B9%D0%BA%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/28d48ba2057cf30db9f9e5badaac5cb4.png)

Все. Теперь достаточно нажать сверху галочку System proxy или VPN mode, и вы попадаете в интернет через ваш новый прокси.

Чтобы настроить в других клиентах или на других устройствах (например, на смартфоне, или поделиться сервером с друзьями), кликаем на сервер правой кнопкой мыши, выбираем Share -> QR code and Link, и получаем ссылку, которую можно отправить кому-нибудь например через Telegram и QR-код, который можно отсканировать камерой во многих клиентах:

![](%D0%9E%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20XRay%20%D0%B4%D0%BB%D1%8F%20Shadowsocks-2022%20%D0%B8%20VLESS%20%D1%81%20XTLS-Vision,%20Websockets%20%D0%B8%20%D1%84%D0%B5%D0%B9%D0%BA%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/c7100d8a383d786dc5393566e8997080.png)

Соответственно, потом на мобильном устройстве в Nekobox, или в v2rayNG, или в Wings X, или в любом другом клиенте, нажимаем что-то типа "Add server" -> "Scan QR" - и все, новый сервер у вас в списке, можно подключаться.

Важно: некоторые клиенты при добавлении сервера по ссылке или QR теряют настройку uTLS, поэтому перепроверяйте все ли на месте после добавления нового сервера.

Лайфхак #1: а еще можно упороться и добавить в Nekobox еще и SSH в качестве подключения, пример конфигурации есть [вот здесь](https://github.com/MatsuriDayo/nekoray/issues/373#issuecomment-1426950636) (сначала надо будет подключиться родным системным ssh-клиентом, [сгенерить клиентский ssh-ключи и сделать ssh-copy-id](https://linuxhint.com/use-ssh-copy-id-command/), в Windows это тоже работает).

Лайфхак #2: Чтобы не забывать ставить uTLS fingerprint для каждого подключения отдельно, его можно задать дефолтное значение в общих настройках Nekobox:

![](%D0%9E%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20XRay%20%D0%B4%D0%BB%D1%8F%20Shadowsocks-2022%20%D0%B8%20VLESS%20%D1%81%20XTLS-Vision,%20Websockets%20%D0%B8%20%D1%84%D0%B5%D0%B9%D0%BA%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/d5c3d0918eb46bb8517dbec3a8344658.png)

### Вариант второй, полуготовый

А теперь представим, что у вас на VPS уже установлен веб-сервер с каким-нибудь сайтом, уже настроены TLS-сертификаты, и все остальное, и нужно просто аккуратно добавить прокси, желательно не ломая конфиг сервера.

Вариант раз: заиметь еще один поддомен, и разруливать TLS-подключения еще на этапе хэндшейка по SNI с помощью, например, HAProxy или [ssl\_preread](http://nginx.org/en/docs/stream/ngx_stream_ssl_preread_module.html) модуля в Nginx. Тогда настройка XRay будет полностью аналогична описанному в предыдущем пункте, разве что только надо будет перевесить его с 443 на другой порт.

Вариант два: TLS-сессия будет терминироваться веб-сервером, и в случае обращения к определенному URL он будет передавать подключение на прокси. Этот вариант проще, единственное ограничение - никакого XTLS (ни Vision, ни Reality) уже не получится, и производительность будет немного ниже.

Итак, допустим, у вас настроен Nginx (или любой другой веб-сервер с каким-нибудь сайтом). Нужно средствами веб-сервера настроить переадресацию обращений к определенному урлу на прокси. Варианта два - использовать websockets (и надо не забыть передать специфичные для них хедеры), или использовать gRPC (если ваш сервер умеет его проксировать). В Nginx это будет выглядеть примерно так, для веб-сокетов:

```
location /myverysecretpath {
         proxy_pass http://127.0.0.1:8888;
         proxy_http_version 1.1;
         proxy_set_header Upgrade $http_upgrade;
         proxy_set_header Connection "Upgrade";
         proxy_set_header Host $host;
       }
```

А конфиг XRay будет таким:

```
{<br>  "log": {<br>    "loglevel": <span>"info"</span><br>  },<br>  "routing": {<br>    "rules": [],<br>    "domainStrategy": <span>"AsIs"</span><br>  },<br>  "inbounds": [<br>    {<br>      "port": <span>23</span>,<br>      "tag": <span>"ss"</span>,<br>      "protocol": <span>"shadowsocks"</span>,<br>      "settings": {<br>        "method": <span>"2022-blake3-aes-128-gcm"</span>,<br>        "password": <span>"aaaaaaaaaaaaaaaabbbbbbbbbbbbbbbb"</span>,<br>        "network": <span>"tcp,udp"</span><br>      }<br>    },<br>    {<br>      "listen": <span>"localhost"</span>,<br>      "port": <span>8888</span>,<br>      "protocol": <span>"vless"</span>,<br>      "tag": <span>"vless_ws"</span>,<br>      "settings": {<br>        "clients": [<br>          {<br>            "id": <span>"7957c33c-d9ca-11ed-afa1-0242ac120002"</span>,<br>            "email": <span>"user1@myserver"</span><br>          }<br>        ],<br>        "decryption": <span>"none"</span><br>      },<br>      "streamSettings": {<br>        "network": <span>"ws"</span>,<br>        "security": <span>"none"</span>,<br>        "wsSettings": {<br>          "path": <span>"/myverysecretpath"</span><br>        }<br>      }<br>    }<br>  ],<br>  "outbounds": [<br>    {<br>      "protocol": <span>"freedom"</span>,<br>      "tag": <span>"direct"</span><br>    },<br>    {<br>      "protocol": <span>"blackhole"</span>,<br>      "tag": <span>"block"</span><br>    }<br>  ]<br>}
```

Как видно, почти то же самое, что и в предыдущем варианте, только нет inbound для "прямого" TLS-подключения, и вообще нет ничего про TLS - сервер слушает 8888 порт и сразу обрабатывает его как веб-сокет. /myverysecretpath, понятное дело, должен совпадать в конфиге веб-сервера и в конфиге прокси.

Настройки клиентов для этого варианта будут полностью аналогичны настройкам клиентов для Shadowsocks и VLESS+Websocket из прошлого пункта.

Вариант с gRPC по примеру из [официальной репы с примерами](https://github.com/XTLS/Xray-examples/tree/main/VLESS-GRPC) у меня так и не заработал (чует мое сердце, там есть какой-то подвох с TLS и с переадресацией на него) - так что если у кого-то есть рабочие конфиги для XRay и Nginx с gRPC, делитесь в комментариях.

### Вариант третий для самых ленивых (Websockets-only)

```
<span>$ apt</span> install docker.io docker-compose<br><span>$ mkdir</span> /etc/xray/<br><span>$ nano</span> /etc/xray/config.json<br><span>$ nano</span> /etc/xray/Caddyfile<br><span>$ nano</span> docker-compose.yml
```

/etc/xray/config.json:

```
{<br>    "log": {<br>        "loglevel": <span>"info"</span><br>    },<br>    "routing": {<br>        "domainStrategy": <span>"AsIs"</span>,<br>        "rules": [<br>            {<br>                "type": <span>"field"</span>,<br>                "ip": [<br>                    <span>"geoip:private"</span><br>                ],<br>                "outboundTag": <span>"block"</span><br>            }<br>        ]<br>    },<br>    "inbounds": [<br>        {<br>            "listen": <span>"0.0.0.0"</span>,<br>            "port": <span>5000</span>,<br>            "tag": <span>"vless_ws"</span>,<br>            "protocol": <span>"vless"</span>,<br>            "settings": {<br>                "clients": [<br>                    {<br>                        "id": <span>"7957c33c-d9ca-11ed-afa1-0242ac120002"</span>,<br>                        "email": <span>"test@test.com"</span><br>                    }<br>                ],<br>                "decryption": <span>"none"</span><br>            },<br>            "streamSettings": {<br>                "network": <span>"ws"</span>,<br>                "security": <span>"none"</span><br>            }<br>        }<br>    ],<br>    "outbounds": [<br>        {<br>            "protocol": <span>"freedom"</span>,<br>            "tag": <span>"direct"</span><br>        },<br>        {<br>            "protocol": <span>"blackhole"</span>,<br>            "tag": <span>"block"</span><br>        }<br>    ]<br>}
```

/etc/xray/Caddyfile

```
example.com <span>{</span><br>  handle_path /myverysecretpath <span>{</span><br>        reverse_proxy http://xray:5000<br>  <span>}</span><br>  reverse_proxy  lib.ru:80 <span>{</span><br>  <span>}</span><br><span>}</span>
```

или, если не нужна переадресация, а хватит просто 401 Unauthrized:

```
example.com {
  handle_path /myverysecretpath {
        reverse_proxy http://xray:5000
  }
  basicauth / {
        bob JDJhJDE0JElab2ZPM25zdU40bE5SSURlTHd3OHVBeVJvYTlMN3dMOEFMdFVCRzNYS1l5ODl6TlVyQllH
  }
}
```
docker-compose.yml

```
<span>version</span><span>: </span><span>'2'</span><br><br><span>volumes</span><span>:</span><br><span>  caddy_data</span><span>:</span><br><span>  caddy_config</span><span>:</span><br><br><span>services</span><span>:</span><br><span>  xray</span><span>:</span><br><span>    image</span><span>: </span>teddysun/xray<br><span>    volumes</span><span>:</span><br><span>      - </span>/etc/xray:/etc/xray<br><span>    ports</span><span>:</span><br><span>      - </span>23:23<br><br><span>  caddy</span><span>:</span><br><span>    image</span><span>: </span>caddy<br><span>    volumes</span><span>:</span><br><span>      - </span>caddy_data:/data<br><span>      - </span>caddy_config:/config<br><span>      - </span>/etc/xray/Caddyfile:/etc/caddy/Caddyfile<br><span>    depends_on</span><span>:</span><br><span>      - </span>xray<br><span>    links</span><span>:</span><br><span>      - </span>xray:xray<br><br><span>    ports</span><span>:</span><br><span>      - </span>443:443<br><span>      - </span>80:80
```

```
$ docker-compose up -d
```

Тут в качестве веб-сервера используется Caddy, он же сам запрашивает и обновляет TLS-сертификаты (certbot не нужен). IPv6 не будет, но все остальное в принципе работает - опять же, только WS, и никакого XTLS. [Lazydocker](https://github.com/jesseduffield/lazydocker) вам в помощь.

### Нюансы и мудрости

На сегодняшний день связка VLESS+XTLS-Vision является самой проверенной и устойчивой к блокировкам. Однако нужно иметь в виду еще пару вещей:

1.  Обязательно используйте uTLS на клиентах, выставляя правильный TLS fingerprint. Клиенты, которые не умеют в uTLS лучше не использовать;
    
2.  Обязательно поднимите фейковый веб-сайт или настройте fallback-переадресацию на какой-нибудь левый адрес;
    
3.  С uTLS связан интересный баг: если при использовании XTLS-Vision вы почему-то не можете подключиться, в логах сервера видна ошибка типа "failed to use xtls-rprx-vision, found outer tls version 771", попробуйте сменить версию uTLS. У меня, например, при выборе "android" клиент не подключается, а при выборе "chrome" все окей;
    
4.  С XTLS лучше, чем без него;
    
5.  Во время отладки конфигурации в случае проблем с TLS может помочь опция "allowInsecure" на клиенте;
    
6.  Очень рекомендуется настраивать на клиентах правила маршрутизации, чтобы трафик до .ru-доменов и хостов с российскими IP шел напрямую, а не через прокси (в клиентах для такого поставляется GeoIP база данных).
    
    GeoIP из Nekobox (да и других клиентов) знает российские диапазоны, а вот GeoSite - уже нет, увы.
    
    В итоге у меня работает вот такая настройка, GeoIP активируется стандартным образом в Nekobox:
    
    ![](%D0%9E%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20XRay%20%D0%B4%D0%BB%D1%8F%20Shadowsocks-2022%20%D0%B8%20VLESS%20%D1%81%20XTLS-Vision,%20Websockets%20%D0%B8%20%D1%84%D0%B5%D0%B9%D0%BA%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/8dda34db2ae52c737dff963e1b291ade.png)
    
    правила по суффиксам Nekobox не умеет, но их умеет sing-box в его основе, поэтому жмем "Custom" и прописываем
    
    вот такое
    
    ```
    {<br>    "rules": [<br>        {<br>            "domain_suffix": [<br>                <span>".ru"</span><br>            ],<br>            "outbound": <span>"direct"</span><br>        }<br>    ]<br>}
    ```
    
    ![](%D0%9E%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20XRay%20%D0%B4%D0%BB%D1%8F%20Shadowsocks-2022%20%D0%B8%20VLESS%20%D1%81%20XTLS-Vision,%20Websockets%20%D0%B8%20%D1%84%D0%B5%D0%B9%D0%BA%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/4281ac160e554a0e0f21809e1987ba95.png)
    
      
    После этого весь трафик до .ru-доменов и российских IP-адресов будет идти напрямую.  
    Если нужно заблокировать полностью - то в первом окне вместо Direct вставить в Block, а в JSON-коде исправить direct на block.
    
      
    Ещё можно иметь два сервера (low-end сервер в РФ можно арендовать рублей так за 60), и приняв подключение передавать его на следущий сервер, указав в outboundTag не freedom и не block, а тег соответствующего outbound'а (XRay может работать сразу и как сервер, и как клиент, не забываем).
    
7.  При проксировании на Nginx или любой другой сервер, так же хорошей практикой считается проксировать HTTP/1.1 и HTTP/2 отдельно.  
    В конфиге Nginx для этого нужно что-то типа такого:
    

```
    listen 127.0.0.1:8888;
    listen 127.0.0.1:8889 http2;
```

А в конфиге XRay:

```
"fallbacks": [
                    {
                        "dest": "8888"
                    },
                    {
                        "alpn": "h2",
                        "dest": "8889"
                    }
]
```

### А что там с CDN?

Пока что известно две CDN, которые позволяют на бесплатных аккаунтах работать с подобным: Cloudflare позволяет проксировать [Websocket](https://developers.cloudflare.com/support/network/using-cloudflare-with-websockets/) и [gRPC](https://developers.cloudflare.com/support/network/understanding-cloudflare-grpc-support/), [GCore](https://gcore.com/) позволяет проксировать Websocket (насчет gRPC не знаю, не проверял). Про Cloudflare говорят, что при проксировании очень больших объемов через вебсокеты они могут попросить перейти на платный тариф, про gRPC такого не написано.

Для работы через CDN нужен будет уже полноценный домен (не DynDNS), который можно делегировать на NS CDN-сети и управлять им там. Дальше нужно включить проксирование для конкретного домена:

![](%D0%9E%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20XRay%20%D0%B4%D0%BB%D1%8F%20Shadowsocks-2022%20%D0%B8%20VLESS%20%D1%81%20XTLS-Vision,%20Websockets%20%D0%B8%20%D1%84%D0%B5%D0%B9%D0%BA%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/70b2aa49e4d9b404bf1a13079d692987.png)

**Лайфхак**: если у вас дешевый IPv6-only сервер, вы можете указать для него только AAAA-запись (IPv6), и Cloudflare все равно позволит вам подключаться к нему по IPv4 через свою сеть. _Смекалочка_.

Ну и не забыть отдельно включить в настройках проксирование WS и gRPC:

![](%D0%9E%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20XRay%20%D0%B4%D0%BB%D1%8F%20Shadowsocks-2022%20%D0%B8%20VLESS%20%D1%81%20XTLS-Vision,%20Websockets%20%D0%B8%20%D1%84%D0%B5%D0%B9%D0%BA%D0%BE%D0%B2%D1%8B%D0%BC%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/ec44d3bdacfedc6dffd09e45eabb8a56.png)

Подробнее: [Особенности проксирования через CDN/Websocket/gRPC для обхода блокировок](https://habr.com/ru/articles/761798/)

### А что с XTLS-Reality?

Технология многообещающая, уже даже во многих клиентах поддерживаемая, но с ней надо разбираться отдельно, и ее настройка - тоже разговор отдельный. Кто уже смог и осилил - пишите в комментарии, а лучше вообще еще одну статью. Источник вдохновения и примеры конфигов с XTLS-Reality [можно найти здесь](https://github.com/chika0801/Xray-examples/).

### А что с Sing-box?

Sing-box - активно развивающийся и тоже многообещающий клиент и сервер, и он может использоваться вместо XRay, поскольку тоже поддерживает Shadowsocks-2022, VLESS, Trojan, XTLS-Vision и XTLS-Reality, а еще умеет работать с Hysteria, Naiveproxy, и всякое другое.

[Официальный сайт](https://xn--singbox-806c.sagernet.org/,)  
[Github](https://github.com/SagerNet/sing%E2%80%91box)  
[Документация по настройке](https://github.com/SagerNet/sing%E2%80%91box/tree/dev%E2%80%91next/docs/configuration)  
Разработчики реорганизуют репу, поэтому переход по ссылкам в документации может выдавать 404 ошибку — без паники, смотрим название файла, и находим правильный путь в гите по названию, дальше никаких проблем.  
Как и XRay, Sing‑box умеет делать fallback'и, только здесь в секции «listen» оно называется «detour», и значением этого параметра должен быть «tag» другого inbound'а.  
[Сайт со скриптами автоустановки и примерами конфигураций](https://vpnrouter.homes/install%E2%80%91singbox/%20%D0%B8%20https://vpnrouter.homes/singbox%E2%80%91ready/)

## А можно проще, и чтобы все и сразу?

Для Xray и сотоварищей существует много разных user-friendly серверных морд с простой установкой.

Есть [Marzban](https://github.com/Gozargah/Marzban), например - его тоже можно ставить через Docker, он включает в себя XRay, обещает красивый интерфейс для настройки и управления пользователями и даже встроенный Telegram-бот.

Ещё есть [Libertea](https://github.com/VZiChoushaDui/Libertea), [Hiddify](https://github.com/hiddify/hiddify-config) (про него сказано что он умеет Reality), и разные [форки](https://github.com/NidukaAkalanka/x-ui-english) X-UI, где обещают все то же самое.

Но тестировать и сравнивать их у меня пока времени и терпения нет :)

На этом всё. Удачи вам в нелегком деле настройки всего этого дела, и да прибудет с вами сила.

Следущая статья серии:  
«[Bleeding-edge обход блокировок: настраиваем сервер и клиент XRay с XTLS-Reality быстро и просто](https://habr.com/ru/articles/731608/)»

Предыдущие статьи серии:

«[Современные технологии обхода блокировок: V2Ray, XRay, XTLS, Hysteria и все‑все‑все](https://habr.com/ru/articles/727868/)»

«[Программы‑клиенты для протоколов недетектируемого обхода блокировок сайтов: V2Ray/XRay, Clash, Sing‑Box, и другие](https://habr.com/ru/articles/728696/)»

Если вы хотите сказать спасибо автору — сделайте пожертвование в один из благотворительных фондов: "[Подари жизнь](https://podari-zhizn.ru/ru)", "[Дом с маяком](https://mayak.help/)", "[Антон тут рядом](https://antontut.ru/)".

**Attention**: Пользователь в Telegram с таким же ником, как у меня — мошенник, не имеющий никакого отношения к автору статьи.

_Копия статьи, на случай если с этой что-то случится:  
_[_https://miracleptr.wordpress.com/2023/04/13/обход-блокировок-настройка-сервера-xray/_](https://miracleptr.wordpress.com/2023/04/13/%d0%be%d0%b1%d1%85%d0%be%d0%b4-%d0%b1%d0%bb%d0%be%d0%ba%d0%b8%d1%80%d0%be%d0%b2%d0%be%d0%ba-%d0%bd%d0%b0%d1%81%d1%82%d1%80%d0%be%d0%b9%d0%ba%d0%b0-%d1%81%d0%b5%d1%80%d0%b2%d0%b5%d1%80%d0%b0-xray/)
