---
created: 2024-02-21T17:06:38 (UTC +06:00)
tags: []
source: https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server
author: 
---

# Краткое руководство по борьбе с DDOS-атаками на веб-сервер | FirstVDS

> ## Excerpt
> Расскажем о том, как выявить проблемный сайт на сервере и восстановить работоспособность сайтов при атаках.

---
DDoS-атаки бывают [разных типов](https://firstvds.ru/technology/ddos-ataki-tipy-atak-i-urovni-modeli-osi) — злоумышленники могут атаковать как сервер в целом, так и отдельный сайт. Сегодня мы подробнее остановимся на атаках уровня L7, его еще называют уровнем приложений. В случае с сайтами, такая атака направлена на http/https-сервер и проявляется, как правило, в том, что на веб-сервере запускается огромное количество процессов — нагрузка возрастает и сервер начинает тормозить или становится недоступен по причине исчерпания ресурсов.

Далее расскажем о том, как выявить проблемный сайт на сервере и восстановить работоспособность сайтов при атаках.

1.  [Диагностика на наличие атаки](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#1)
    1.  [Определяем наличие атаки](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#11) 
    2.  [DoS-атаки и выявление источника атаки по логам](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#12)
    3.  [Простой, но важный способ снижения нагрузки от атак](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#13)
2.  [Защита от DDoS-атак](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#2)
    1.  [С помощью ISPmanager](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#21)
        1.  [Точечная блокировка в ISPmanager](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#211)
        2.  [Блокировка по странам](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#211)
    2.  [Ручные настройки](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#22) 
        1.  [Корректировка параметров apache](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#221)
        2.  [Модули nginx](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#222)
        3.  [Переменные sysctl](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#223)
3.  [Подключение DDoS-защиты](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#3)
    1.  [Если нужен сервис защиты, но бюджет не позволяет](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#31)

## [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#1)Диагностика на наличие атаки 

В первую очередь нам необходимо понять, есть ли DDoS-атака.

Если это атака на канальном уровне, то, скорее всего, ваш сервер будет недоступен, так как сетевой канал забит, и попасть на сервер можно будет только через VNC.  
Однако в данной статье мы говорим об атаках на веб-сервер — в этом случае, если есть возможность — зайдите на сервер по ssh.

### [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#11)Определяем наличие атаки

Командами ps и top вы можете выявить главный признак атаки — большое количество процессов `httpd (apache2), php-fpm или nginx (реже).` 

Если на сервер по ssh войти не удается, то вполне возможно еще удастся попасть через окно VNC в панели VMmanager (бессетевой доступ к VDS).

Также можно перезагрузить сервер через VMmanager и попробовать зайти по ssh. Если возрастает количество процессов веб-сервера, то необходимо остановить его либо, как вариант, можно «убить» все процессы веб-сервера командой:

`killall -9 <имя демона-процесса веб-сервера>`

Посмотреть, какой процесс запускается в качестве веб-сервера, можно с помощью команды (если утилиты netstat нет, то необходимо предварительно ее установить):

`netstat -na | grep ':80 '`

`netstat -na`

Посмотреть количество процессов веб-сервера и количество подключений на 80-ый порт:

`ps aux | grep -с <имя демона-процесса веб-сервера>`

`netstat -na | grep -с ":80 "`

Эти команды считают количество процессов веб-сервера и количество подключений на 80 порт **(если у вас используется SSL-сертификат, то вместо “80” необходимо указывать “443”, оптимально использовать оба варианта)**.

Если количество соединений превышает среднестатистическое, вероятно, это DoS/DDoS-атака.

### [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#12)DoS-атаки и выявление источника атаки по логам 

DoS-атаки случаются даже чаще,чем DDoS, и если говорить максимально упрощенно, то их отличает то, что они:

-   либо направлены с одного источника,
-   либо не приводят к полной недоступности ресурса — только к высокой нагрузке,
-   либо их основная цель использовать уязвимость на сайте или ином веб-ресурсе.

Посмотреть, какие источники подключений к 80-му (443-му) порту присутствуют, можно с помощью команды:

`netstat -an | grep -E '\:80 |\:443 '| awk '{print $5}' | grep -Eo '[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}' | sort | uniq -c | sort -n`

Команда выведет список по возрастанию в виде строк: количество подключений — ip-адрес источника.

Данная команда также поможет в выявлении DoS/DDoS. Tcpdump запишет в файл ddos.log первые 200 пакетов, которые соединений на 80/443 порту:

`tcpdump -nr ddos.log | awk '{print $3}' |grep -oE '[0-9]{1,}\.[0-9]{1,}\.[0-9]{1,}\.[0-9]{1,}' |sort |uniq -c |sort -rn`

Если атака временно прекратилась или вы хотите узнать, откуда больше всего подключались, можно проанализировать access-лог веб-сервера командой:

``grep `date +%d/%b/%Y` /var/www/httpd-logs/*.access.log  | awk '{print $1}' | sort | uniq -c | sort -nr | head -n 10``

Она выведет список 10 наиболее «активных» IP-адресов. Также по логу можно выявить, на какой сайт идет очень много обращений.

Выяснить, не было ли однотипных обращений, можно командой:

``grep `date +%d/%b/%Y` /var/www/httpd-logs/*.access.log | awk '{print $1" "$7}' | sort | uniq -c | sort -rnk1 | head -n 10``

Для этой же цели многие предпочитают использовать модуль mod\_status.

Mod\_status позволяет в реальном времени осуществлять мониторинг загрузки сервера.

Например, в случае apache 2.4 необходимо отредактировать файл `/etc/apache2/mods-enabled/status.conf`

Поменять секцию:

```
      &lt;Location /server-status&gt;

               SetHandler server-status

               Require local

               #Require ip 192.0.2.0/24

       &lt;/Location&gt;
```

на

```
      &lt;Location /server-status&gt;

               SetHandler server-status

       &lt;/Location&gt;
```

Затем проверить, чтобы следующая строка строка была не закомментирована, т.е. присутствовала в файле именно в таком виде:

`ExtendedStatus On`

Перезапустите apache.

Теперь по адресу [http://ip-aдрес.сервера/server-status](http://xn--ip-a-t4dg0fj.xn--80adib7ccc/server-status) доступна статистика запросов к нему.

Нас интересует следующее:

```
0-0    86795    0/31/31    W     0.54    0    0    0.0    0.09    0.09     89.18.166.89    example.com    GET / HTTP/1.0

1-0    86796    0/19/19    W     0.30    0    0    0.0    0.06    0.06     79.140.78.68    example.com    GET / HTTP/1.0

2-0    86801    0/9/9    W     0.15    0    0    0.0    0.03    0.03     89.18.166.89    example.com    GET / HTTP/1.0

3-0    86802    0/9/9    W     0.14    0    0    0.0    0.03    0.03     82.12.33.48    myhost.com    GET /server-status/ HTTP/1.1

4-0    86805    0/4/4    W     0.07    1    0    0.0    0.01    0.01     79.140.78.68    example.com    GET / HTTP/1.0

5-0    86806    0/3/3    W     0.06    2    0    0.0    0.01    0.01     89.18.166.89    example.com    GET / HTTP/1.0

6-0    86807    0/4/4    W     0.07    0    0    0.0    0.01    0.01     89.18.166.89    example.com    GET / HTTP/1.0

7-0    86808    1/4/4    C     0.08    0    2015    3.0    0.01    0.01     89.18.166.89    example.com    GET / HTTP/1.0

8-0    86811    0/1/1    W     0.02    0    0    0.0    0.00    0.00     89.18.166.89    example.com    GET / HTTP/1.0

9-0    86812    0/1/1    W     0.02    0    0    0.0    0.00    0.00     89.18.166.89    example.com    GET / HTTP/1.0

10-0    86813    0/1/1    W     0.02    0    0    0.0    0.00    0.00     89.18.166.89    example.com    GET / HTTP/1.0 
```

В данном случае атака идет пустыми запросами GET / к серверу example.com. Вам следует его отключить. Выполнить это лучше всего в панели управления: зайдя в раздел с сайтами, выбрать сайт (в данном случае example.com) и нажать сверху кнопку `Файлы конфигурации`, где вы увидите внизу конфигурационный файл nginx. Найдите отрывок такого вида:

```
server {

       listen       80;

       server_name  example.com www.example.com;
```

Пропишите ниже `server_name` строку

`deny all;`

Аналогично сделайте в секции, где указано `listen 443`;

Если у вас нет nginx, а только apache, то перейдите в корневую директорию сайта и создайте файл .htaccess с содержимым:

`Deny from All`

Теперь при обращении к сайту будет выдаваться ошибка `403 Forbidden`, и нагрузка на остальные сайты значительно снизится.

### [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#13)Простой, но важный способ снижения нагрузки от атак

Как правило, флуд-боты обращаются по доменному имени, но могут перебирать и IP-адреса. В этом случае необходимо [создать домен-заглушку](https://firstvds.ru/technology/kak-dobavit-sayt-zaglushku-na-server). Но не забудьте для него в **файлах конфигурации** nginx указать `deny all;` (как в примере выше) или, если у вас только apache, в корневой директории заглушки добавить такой же файл .htaccess с содержимым:

`Deny from All`

Это не самый эффективный способ борьбы с атаками, но крайне полезный, так как он решает помимо этого много проблем с индексацией сайта, со сканом сайтов по IP-адресу и другие случаи. Однако далее мы рассмотрим уже целенаправленные способы фильтрации атак.

## [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#2)Защита от DDoS-атак

### [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#21)С помощью ISPmanager

В панели ISPmanager есть функция, позволяющая ограничить количество подключений к сайту с одного IP-адреса. При этом IP-адрес блокируется по всем портам, поэтому если ваш IP-адрес попадёт в бан, то вы потеряете связь с сервером на 5 минут.

Приступаем к настройке.

Заходим в ISPmanager — раздел «Сайты» (в старом меню это «Домены-WWW-домены») — выделить домен — кнопка «Изменить». Ищем подраздел «Оптимизация» и «защита от DDos» и ставим галочку «Включить защиту от DDoS-атаки» — следом появятся параметры защиты от атаки.

[

![](%D0%9A%D1%80%D0%B0%D1%82%D0%BA%D0%BE%D0%B5%20%D1%80%D1%83%D0%BA%D0%BE%D0%B2%D0%BE%D0%B4%D1%81%D1%82%D0%B2%D0%BE%20%D0%BF%D0%BE%20%D0%B1%D0%BE%D1%80%D1%8C%D0%B1%D0%B5%20%D1%81%20DDOS-%D0%B0%D1%82%D0%B0%D0%BA%D0%B0%D0%BC%D0%B8%20%D0%BD%D0%B0%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%20%20FirstVDS/ddos1.png)

](https://firstvds.ru/sites/default/files/images/technology/ddos1.png)

А именно:

**«Количество запросов в секунду»** — при достижении указанного количества подключений, IP блокируется только в Nginx.  
**«Максимальный размер всплеска»** — при достижении указанного количества подключений, IP блокируется по всем портам на 5 минут.

**Обратите внимание!** Данный модуль всего лишь метод сглаживания слабых атак силами веб-сервера. Если атака серьезная и полностью выводит из строя веб-сервер своей нагрузкой, то данный модуль никак не спасет. Также не указывайте экстремально низкие значения (меньше 30-20), так как архитектура некоторых сайтов подразумевает, что при одном клике на сайте к серверу уйдет несколько запросов, следовательно, повышается риск заблокировать легитимного пользователя.

#### [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#211)Точечная блокировка в ISPmanager и блокировка по странам

Выше мы уже описывали варианты поиска источника нагрузки, когда атакующих IP-адресов немного. В этом случае их проще всего точечно заблокировать в панели ISPmanager в разделе «Администрирование» — «Брандмауэр», нажав кнопку «Создать» и указав адрес:

[

![](%D0%9A%D1%80%D0%B0%D1%82%D0%BA%D0%BE%D0%B5%20%D1%80%D1%83%D0%BA%D0%BE%D0%B2%D0%BE%D0%B4%D1%81%D1%82%D0%B2%D0%BE%20%D0%BF%D0%BE%20%D0%B1%D0%BE%D1%80%D1%8C%D0%B1%D0%B5%20%D1%81%20DDOS-%D0%B0%D1%82%D0%B0%D0%BA%D0%B0%D0%BC%D0%B8%20%D0%BD%D0%B0%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%20%20FirstVDS/ddos2.png)

](https://firstvds.ru/sites/default/files/images/technology/ddos2.png)

В этом же разделе есть возможность включить блокировку по определенным странам, нажав сверху кнопку «Страны». Стоит учитывать, что географическая привязанность IP-адресов всегда условна и может быть не точна. По этой причине для более-менее рабочей точности геораспределения адреса используются платные базы IP-адресов, в ISPmanager это работает именно так, и блокировка будет доступна, если вы зарегистрируетесь и приобретете ключ доступа к базам сервиса Maxmind:

[

![](%D0%9A%D1%80%D0%B0%D1%82%D0%BA%D0%BE%D0%B5%20%D1%80%D1%83%D0%BA%D0%BE%D0%B2%D0%BE%D0%B4%D1%81%D1%82%D0%B2%D0%BE%20%D0%BF%D0%BE%20%D0%B1%D0%BE%D1%80%D1%8C%D0%B1%D0%B5%20%D1%81%20DDOS-%D0%B0%D1%82%D0%B0%D0%BA%D0%B0%D0%BC%D0%B8%20%D0%BD%D0%B0%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%20%20FirstVDS/ddos3.png)

](https://firstvds.ru/sites/default/files/images/technology/ddos3.png)

[

![](%D0%9A%D1%80%D0%B0%D1%82%D0%BA%D0%BE%D0%B5%20%D1%80%D1%83%D0%BA%D0%BE%D0%B2%D0%BE%D0%B4%D1%81%D1%82%D0%B2%D0%BE%20%D0%BF%D0%BE%20%D0%B1%D0%BE%D1%80%D1%8C%D0%B1%D0%B5%20%D1%81%20DDOS-%D0%B0%D1%82%D0%B0%D0%BA%D0%B0%D0%BC%D0%B8%20%D0%BD%D0%B0%20%D0%B2%D0%B5%D0%B1-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%20%20FirstVDS/image1%252819%2529.png)

](https://firstvds.ru/sites/default/files/images/image1%2819%29.png)

### [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#22)Ручные настройки

Чтобы точечно зафильтровать 80 порт для ip xxx.xxx.xxx.xxx, используйте команду:

`iptables -A INPUT -p tcp --src xxx.xxx.xxx.xxx --dport 80 -j DROP`

Эти и последующие команды можно и нужно аналогично использовать и для 443-го порта.  
Можно использовать ipset (нужен соответствующий модуль):

```
ipset -N ddos iphash

iptables -A INPUT -p tcp -m tcp --dport 80 -m set --set ddos src -j DROP

while true; do tail -10000 /var/www/httpd-logs/site.ru.access.log | sort | cut -f 1 -d " " | uniq -c | awk '($1&gt;50){print $2}' | xargs -tl -I _ ipset -A ddos _;sleep 30; done
```

Закрываем icmp (поможет при icmp-flood):

`iptables -A INPUT -p icmp -j DROP --icmp-type 8`

Ограничение максимального числа «полуоткрытых» соединений с одного IP к конкретному порту (необходим соответствующий модуль):

`iptables -I INPUT -p tcp --syn --dport 80 -m iplimit --iplimit-above 10 -j DROP`

Ограничение максимального числа соединений с одного IP к конкретному порту:

`/sbin/iptables  -A INPUT -p tcp --syn --dport 80 -m connlimit --connlimit-above 3 -j REJECT`

#### [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#221)Корректировка параметров apache

Если в качестве веб-сервера используется apache, в качестве дополнительных средств можно корректировать параметры, влияющие на количество создаваемых процессов в системе.

Откройте любым удобным текстовым редактором конфигурационный файл `/etc/httpd/conf/httpd.conf` (если Centos) и `/etc/apache2/mods-available/mpm_prefork.conf` (если Debian/Ubuntu):

```
&lt;IfModule mpm_itk_module&gt;

   StartServers          1

   MinSpareServers       1

   MaxSpareServers       1

   MaxClients          150

   MaxRequestsPerChild  100

&lt;/IfModule&gt;
```

Измените MaxClients (максимальное число процессов) в меньшую сторону до количества процессов, которые не забивают ресурсы вашего сервера до отказа. Каждый процесс apache занимает в среднем 4-8 мегабайт памяти. А лучше узнать точно, сколько памяти в среднем на вашем сервере занимает один процесс apache/httpd так:

`top -d 1 | grep -E 'http|apache' | awk '{a+=$7}END{print a/(1024*NR) " Mb"}'`

Необходимо, чтобы суммарное количество помещалось в оперативной памяти.

Отредактируйте `/etc/httpd/conf/httpd.conf` (если Centos) и `/etc/apache2/apache2.conf` (если Debian/Ubuntu)

Следует уменьшить параметры:

`Timeout 300`

`MaxKeepAliveRequests 100`

до 60 и 50 соответственно, чтобы как можно скорее старые процессы apache прекращали свое существование.

#### [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#222)Модули Nginx

Если панели ISPmanager нет, но используется веб-сервер nginx, проверьте, чтобы nginx был собран с модулями http limit req и GeoIP. С их помощью аналогичным образом, как в панели ISPmanager, только вручную в конфигурационных файлах, можно блокировать доступ для IP-адресов, превысивших ограничение на количество подключений. Также можно заблокировать и доступ по странам. Ввиду возможных особенностей ручных конфигураций, рекомендуем самостоятельно изучить документацию nginx по модулю [HTTP limit req](http://nginx.org/en/docs/http/ngx_http_limit_req_module.html) и [GeoIP](http://nginx.org/en/docs/http/ngx_http_geoip_module.html).

#### [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#223)Переменные sysctl

Системные переменные, которые могут быть полезны, при DDoS.

ICMP-FLOOD

Команда ниже поможет при отражении атаки типа icmp-flood:

`sysctl net.ipv4.icmp_echo_ignore_all=1`

SYN-FLOOD

Уменьшает время удержания «полуоткрытых» соединений:

`sysctl -w net.ipv4.tcp_synack_retries=1`

Включает TCP syncookies:

`sysctl -w net.ipv4.tcp_syncookies=1`

Увеличивает размер очереди полуоткрытых соединений, по умолчанию — 512:

`sysctl -w net.ipv4.tcp_max_syn_backlog=4096`

Проверяет TCP-соединение каждую минуту. Если на другой стороне — легальная машина, она сразу ответит. По умолчанию — 2 часа:  
`sysctl -w net.ipv4.tcp_keepalive_time=60`

Повторяет проверку через 20 секунд:

`sysctl -w net.ipv4.tcp_keepalive_intvl=20`

Количество проверок перед закрытием соединения:

`sysctl -w net.ipv4.tcp_keepalive_probes=3`

Изменяет время ожидания приема FIN:

`sysctl -w net.ipv4.tcp_fin_timeout=10`

Обратите внимание! Эти настройки будут работать до первой перезагрузки сервера, для постоянного использования параметры необходимо внести в системный конфиг sysctl.conf

## [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#3)Подключение DDoS-защиты

Этот раздел статьи должен был быть, пожалуй, самый первым, так как является самым эффективным и главным способом бороться с DDoS-атаками.  
Как уже упоминалось, указанные выше меры могут спасти только от небольших атак или атак с источников-«одиночек». 

Для качественной защиты и избавления от дополнительных манипуляций стоит подключать профессиональную DDoS-защиту. Есть много сервисов, которые не привязаны к хостингу и могут помочь защитить ваш сайт.

Мы, в свою очередь, предлагаем готовое решение от наших партнеров DDoS-Guard по подключению защищенного канала (для атак на канальном уровне) и защиты L7 (для атак на уровне приложений), обе защиты по одной стоимости, подробнее [здесь](https://firstvds.ru/ddos-protection).

#### [](https://firstvds.ru/technology/kratkoe-rukovodstvo-po-borbe-s-ddos-atakami-na-veb-server#31)Если нужен сервис защиты, но бюджет не позволяет

Стоит упомянуть и еще один сервис, который допускает вариант бесплатной защиты от DDoS (как вы понимаете, «бесплатно» означает, что это будет не настолько же эффективно) — [Cloudflare](https://www.cloudflare.com/ru-ru/). 

Базово, на этом сервисе можно добавить свой домен, направив его на предоставляемые ими NS. Также по умолчанию, направив домен на их NS, в качестве А-записей на стороне Cloudflare предлагается указать «проксирующий» IP-адрес, который будет отдаваться по домену глобально и принимать на себя все запросы и пропускать их через фильтр бесплатной защиты. 

Помимо того, что более эффективная фильтрация будет платной, есть еще «узкое горлышко» в том, что сам VDS при наличии доменов с Cloudflare не избавляется от угрозы прямой атаки по IP-адресу. Как правило, IP-адрес скрывают, стараются, чтобы по доменам нигде не «светились» старые DNS-записи, ведущие сразу на IP-адрес VDS и т.д.

Но есть более кардинальное и рекомендуемое решение в данном случае — запретить в фаерволе доступ по 80-му и 443-му портам для всех адресов, кроме адресов из подсетей cloudflare, тем самым пуская к веб-серверу только трафик, уже прошедший фильтрацию сервиса.

На примере Iptables это можно сделать следующими командами:

`iptables -I INPUT -p tcp -m multiport --dports 80,443 -j DROP`

`ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -j DROP`

`wget -O - https://www.cloudflare.com/ips-v4 | while read subnet; do iptables -I INPUT -s $subnet -j ACCEPT; done`

`wget -O - https://www.cloudflare.com/ips-v6 | while read subnet; do ip6tables -I INPUT -s $subnet -j ACCEPT; done`

Это только базовая настройка, которую можно кастомизировать, в том числе и для случаев без Cloudflare, ограничив доступ атакующих источников, или ограничить доступ к DNS, и по почтовым портам, добавив правила для доступа ваших рабочих и других легитимных IP-адресов и т.д.

В целом, грамотная настройка фаервола и веб-сервера может помочь справляться с базовыми атаками даже без участия Cloudflare. Однако никакие бесплатные решения не помогут от серьезных и мощных DDoS-атак.
