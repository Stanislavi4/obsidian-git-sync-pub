---
created: 2024-02-20T01:18:24 (UTC +06:00)
tags: [обход блокировок,mikrotik,routeros,обход блокировок по доменам,bgp,точечный обход блокировок]
source: https://habr.com/ru/articles/775110/
author: itdog
---

# Точечный обход блокировок на Mikrotik: BGP и Address lists + Mangle. Как реализовать обход по доменам? / Хабр

> ## Excerpt
> Как загрузить список из 500 доменов на mikrotik, чтоб он их преобразовал в IP-адреса?Как заставить Mikrotik резолвить домены по wildcard?Как поднять Shadowsocks\...

---
-   Как загрузить список из 500 доменов на mikrotik, чтоб он их преобразовал в IP-адреса?
    
-   Как заставить Mikrotik резолвить домены по wildcard?
    
-   Как поднять Shadowsocks\\VLESS на ROS?
    

~Никак~

Я задался этими вопросами, достал hAP ac lite из шкафа и посмотрел, на что способна ROS 7 в конце 2023 года. Потестил всем известные способы, а также попробовал запихать много доменов в address-lists.

**UPD 13.12.2023** Стало известно о способе добавления домена в DNS static и складывание зарезолвенных IP-адресов в address list. При этом можно использовать wildcard. Подробности под заголовком "Список доменов в DNS static, wildcard".

## Видеоверсия

## Настройка WireGuard в ROS 7

Для начала вам нужен туннель, в который будет направляться трафик так или иначе заблокированных ресурсов. По настройке туннелей в ROS много мануалов. Я просто приведу пример настройки WG, именно по нему нормальный ман нелегко найти. Хотя сложного ничего тут нет.

В address нужно указать IP-адрес с подсетью (пример 10.7.0.2/24), в network подсеть (пример 10.7.0.0).

```
/interface wireguard
add listen-port=51820 mtu=1420 name=wg0 private-key="$PRIVATE"
/interface wireguard peers
add allowed-address=0.0.0.0/0 comment=wireguard endpoint-address=$HOST endpoint-port=51820 interface=wg0 persistent-keepalive=25s \
    preshared-key="$PRESHARED" public-key="$PUBLIC"
/ip address
add address=$IP interface=wg0 network=$NETWORK
/ip firewall nat
add action=masquerade chain=srcnat out-interface=wg0
```

Обязательно нужно задать параметр persistent-keepalive, без него туннель не поднимется.

Проверить, что трафик ходит через интерфейс

```
/ping habr.com interface wg0
```

Ещё можно глянуть, что значения Rx и Tx ненулевые в **WireGuard - Peers**

![](%D0%A2%D0%BE%D1%87%D0%B5%D1%87%D0%BD%D1%8B%D0%B9%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%20Mikrotik%20BGP%20%D0%B8%20Address%20lists%20+%20Mangle.%20%D0%9A%D0%B0%D0%BA%20%D1%80%D0%B5%D0%B0%D0%BB%D0%B8%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D1%8C%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%20%D0%BF%D0%BE%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%B0%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/3ozvnaa5rjx9mgwiuifunyhpahe.png)

Теперь можно настраивать списки и маршрутизацию.

Какие способы обхода блокировок известны на текущий день для RouterOS?

## BGP. Классика для Mikrotik

Есть два сервиса, которые предоставляют услугу анонса заблокированных IP-адресов и подсетей:

-   [https://antifilter.download/](https://antifilter.download/)
    
-   [https://antifilter.network/](https://antifilter.network/)
    

Первый возник в 2018, второй в 2019 году. Создатели antifilter.network не особо запаривались с неймингом, но запарились с реализацией сервиса.

Оба имеют списки:

-   Все IP-адреса, блокируемые РКН
    
-   Их суммаризация для сокращения количества префиксов
    
-   Блокируемые подсети
    

Чем отличаются?

-   У antifilter.download есть список community. Он создаётся из резолвинга доменов, которые добавили пользователи сервиса
    
-   У antifilter.network есть прикольная фича по выбору списков, не прибегая к фильтрации префиксов на роутере
    
-   У antifilter.network есть список с суммаризацией от /32 до /23 маски
    
-   antifilter.network имеет много других списков
    

У обоих проектов есть чаты, где вы можете задать свой вопрос.

### Antifilter.download

Настроим получение префиксов из списка subent+ipsum+community от antifilter.download. Эти списки отдаются по умолчанию.

Благодарю [@talifan](https://habr.com/users/talifan) за [конфиг](https://habr.com/ru/articles/549282/#comment_24184087) для ROS 7.

Требуется подставить переменные:

-   YOUR\_AS - ваша рандомная AS. Число в диапазоне 64512-65543, кроме 65432
    
-   IP - ваш внешний IP-адрес для того, чтобы не пересекаться с другими пользователями
    
-   INTERFACE - имя VPN интерфейса, через который необходимо пустить трафик
    

```
/routing bgp template
add as=$YOUR_AS disabled=no hold-time=4m input.filter=bgp_in .ignore-as-path-len=yes keepalive-time=1m multihop=yes name=antifilter routing-table=main
/routing bgp connection
add disabled=no hold-time=4m input.filter=bgp_in .ignore-as-path-len=yes keepalive-time=1m local.role=ebgp multihop=yes name=antifilter_bgp remote.address=45.154.73.71/32 .as=65432 router-id=$IP routing-table=main templates=antifilter
/routing filter rule
add chain=bgp_in disabled=no rule="set gw $INTERFACE; accept;"
```

Проверить, что префиксы пришли, можно командой

```
/routing/bgp/session print
```

Значения prefix-count и messages должны быть не равны нулю.

Либо через winbox\\веб-интерфейс. **Routing - BGP - Sessions**, у поднятой сессии раздел Stats.  

![](%D0%A2%D0%BE%D1%87%D0%B5%D1%87%D0%BD%D1%8B%D0%B9%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%20Mikrotik%20BGP%20%D0%B8%20Address%20lists%20+%20Mangle.%20%D0%9A%D0%B0%D0%BA%20%D1%80%D0%B5%D0%B0%D0%BB%D0%B8%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D1%8C%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%20%D0%BF%D0%BE%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%B0%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/t9ezo9hdupzsc2yxa7iigbsyww0.png)

Делайте трассировку с клиента роутера до заблокированного ресурса, маршрут должен проходить через туннель.

> Небольшое пояснение перед следующим абзацем. У antifilter.download есть community список, который составляют сами пользователи. Также в BGP существует понятие community, применятся для маркировки части префиксов. В рассматриваемом контексте используется для разделения и использования разных списков. Не запутайтесь.

По умолчанию отдаются вместе subnet, ipsum и community списки.

Чтоб получать только список community, необходимо отредактировать filter rule:

```
/routing filter rule
add chain=bgp_in disabled=no rule="set gw wg0; if (bgp-communities includes 65432:500) {accept} else {reject}"
```

Остальные BGP community можно посмотреть на сайте в разделе **ЧаВо - "Какие комьюнити используются в BGP-фиде?"**.

В случае фильтрации будут прилетать все префиксы, но активны будут только из выбранного community.

Посмотреть можно в **ip - route**

```
/ip/route print
```

-   Dab - active, те, что используются для маршрутизации
    
-   DbI - inactive, те, что отфильтровались и для маршрутизации не используются
    

Если ваш провайдер блокирует BGP, можно сделать маршрут через VPN для связи с сервисом.

```
/ip route add dst-address=45.154.73.71/32 gateway=$INTERFACE
```

Информация взята из [статьи](https://habr.com/ru/articles/549282/)[@Furriest](https://habr.com/users/Furriest), автора antifilter.download.

### Antifilter.network

Настройка получения префиксов с antifilter.network очень похожа.

Основная информация находится на странице [https://antifilter.network/bgp](https://antifilter.network/bgp).

На этой же странице выбирайте списки, которые вам нужны, проходите reCAPTCHA и нажимаете **Применить**. Теперь к вашему IP-адресу и указанной там AS привязана отдача только выбранных списков. Хитро и очень прикольно сделано.

Настройка такая же, как для antifilter.download, отличаются только Remote Address, Remote AS и вашей AS. А router-id должен содержать IP-адрес, на который вы применили настройки. Эти настройки берутся с их страницы BGP. Фильтровать по community тут не нужно, поэтому правило фильтрации просто с `accept;`.

С настроенной BGP сессией на роутере, после нажатия на кнопку **Применить** уже через несколько секунд прилетают выбранные маршруты.

Делайте трассировку с клиента роутера до заблокированного ресурса, маршрут должен проходить через туннель.

### Как выключить обход блокировок при BGP?

**Routing - BGP - Connection**, кнопка Disable у поднятого конекшена

### Мой опыт с BGP и подводные камни

Главная проблема - это суммаризация. Она нужна, чтобы не было сотен тысяч адресов в Routes. Но в этом случае в туннель также улетают некоторые не заблокированные ресурсы. И это может быть проблемой. Разберём два примера, и что с этим делать.

#### Пример первый. Сайт habr.com

У меня [habr.com](http://habr.com/) резолвится в `178.248.237.68`. Этот IP-адрес из подсети `178.248.236.0/23`, которая присутствует во всех трёх списках с суммаризацией.

В качестве туннеля используется WG, сайт идёт через туннель и не открывается. Соединение отваливается по таймауту, tcpdump показывает, что IP-адрес `178.248.237.68` на запрос к нему отдаёт пару пакетов по HTTPS протоколу.

Дело в том, что эта подсеть принадлежит компании Qrator Labs, которая предоставляет DDOS-защиту. Что-то не нравится какой-то из их систем. В этом случае [решение](https://olegon.ru/showthread.php?t=38451) подсказал [@olegon-ru](https://habr.com/users/olegon-ru).

Дело в MSS у WireGuard. Правится следующим правилом:

```
/ip firewall mangle
add action=change-mss chain=forward new-mss=1380 out-interface=$INTERFACE protocol=tcp tcp-flags=syn tcp-mss=1381-65535
```

Таким образом продолжаем ходить на Хабр через туннель, ничего страшного, только поломаем немного Хабру аналитику.

#### Пример второй. Сайт leroymerlin.ru

Без правки MSS история такая же. Потому что Леруа лежит тоже за Qrator Labs. После правки MSS сайт открывается и вещает:

![](%D0%A2%D0%BE%D1%87%D0%B5%D1%87%D0%BD%D1%8B%D0%B9%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%20Mikrotik%20BGP%20%D0%B8%20Address%20lists%20+%20Mangle.%20%D0%9A%D0%B0%D0%BA%20%D1%80%D0%B5%D0%B0%D0%BB%D0%B8%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D1%8C%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%20%D0%BF%D0%BE%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%B0%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/pok4zmwpbydhjg66eqruxpjwau4.png)

Тут очевидно: надо выкинуть эту подсеть из роута.

Пример с фильтрацией одного префикса для BGP. Правим **routes - filter - rule** для BGP меняем поле rule:

```
set gw wg0; if (dst in 178.248.232.0/22) {reject} else {accept}
```

Для исключения двух подсетей используется логический оператор OR. Тут вторая подсеть как раз та, в которой находится Хабр:

```
set gw wg0; if (dst in 178.248.236.0/23 || dst in 178.248.232.0/22) {reject} else {accept}
```

![](%D0%A2%D0%BE%D1%87%D0%B5%D1%87%D0%BD%D1%8B%D0%B9%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%20Mikrotik%20BGP%20%D0%B8%20Address%20lists%20+%20Mangle.%20%D0%9A%D0%B0%D0%BA%20%D1%80%D0%B5%D0%B0%D0%BB%D0%B8%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D1%8C%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%20%D0%BF%D0%BE%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%B0%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/bqkyieeyeeg0csmew1ensefiqgg.png)

В процессе тестов я обнаружил, что фильтрация на сервисе настроена, скорее всего, по спискам подсетей известных ДЦ. Ситуация, описанная выше, была с известным европейским ДЦ. А когда я использовал, видимо, свежий и "не запаленный" американский IP-адрес, то оба сайта открылись через VPN без проблем. Сайт Леруа не смутил американский IP-адрес, а для Хабра не понадобился костыль с правкой MSS для WG.

#### Может, без суммаризации тогда?

На обоих сервисах рядом со списками ip.lst упомянуто "Использовать с осторожностью". Ради теста я решил помучать мой hAP lite ac. От 130548 префикса в routes ему стало плохо.

## Выкачка списков IP-адресов и подсетей в Address List

Если по какой-то причине вы не используйте BGP, то списки можно выкачивать скриптом и загружать в Address Lists. Но в нашем случае списки, которые можно получить по BGP, лучше получать по BGP. По BGP они добавляются в routes. А скрипт кладёт их в address-lists. Дело в том, что address-list + mangle потребляет больше ресурсов, чем routes.

Но этот метод тоже рабочий, и поэтому его тоже рассмотрим. Тут логика стандартная: есть список адресов, весь трафик, который идёт к IP-адресам из этого списка мы маркируем и весь маркированный трафик отправляется в туннель.

Начнём с формирования списка, списки будем брать у antifilter.network.

Для выкачки списков нужен скрипт. На [официальном форуме Mikrotik](https://forum.mikrotik.com/viewtopic.php?p=935938#p935938) гуру скриптов для ROS уже 4 года вылизывают скрипт для скачивания больших списков. Его и возьмём.

Скрипт большой, поэтому под спойлером.

```
{
/ip firewall address-list
:local update do={
 :put "Starting import of address-list: $listname"
 :if ($nolog = null) do={:log warning "Starting import of address-list: $listname"}
 
 :local displayed true
 :local maxretry 3
 :local retrywaitingtime 120s
 :local retryflag true
 :for retry from=1 to=$maxretry step=1 do={
  :if (retryflag) do={ :set $retryflag false; :set $sounter 0
  :if (retry &gt; 1) do={
   :put "Source file changed. Retring after a $retrywaitingtime wait..."
   :if ($nolog = null) do={:log warning "Source file changed. Retring after a $retrywaitingtime wait..."}
   :delay $retrywaitingtime  }
  
  :local fetchResult [/tool fetch url=$url keep-result=no as-value]
  :local filesize ($fetchResult-&gt;"total")
  :local downsize ($fetchResult-&gt;"downloaded") 
  :if ($filesize = 0 &amp;&amp; $downsize &gt; 0) do={ :set $filesize $downsize}

  :local start 0
  :local maxsize 64000;        # reqeusted chunk size
  :local end ($maxsize - 1);# because start is zero the maxsize has to be reduced by one
  :local partnumber ($filesize / ($maxsize / 1024)); # how many chunk are maxsize
  :local remainder ($filesize % ($maxsize / 1024)); # the last partly chunk 
  :if ($remainder &gt; 0)    do={ :set $partnumber ($partnumber + 1) }; # total number of chunks
  :if ($heirule != null) do={:put "Using as extra filtering: $heirule"} else={:set $heirule "."}
 # remove the current list completely if "erase" is not present (default setting)
  :if ($noerase = null) do={  
   :if ($timeout = null) do={:set $timeout 00:00:00; :do {:foreach i in=[/ip firewall address-list find list=$listname] do={/ip firewall address-list set list=("backup".$listname) $i }} on-error={} } else={
   :do {:foreach i in=[/ip firewall address-list find list=$listname dynamic] do={/ip firewall address-list set list=("backup".$listname) $i }} on-error={} };                
   :put ("Conditional deleting all".$dynamic." entries in address-list: $listname")
   :if ($nolog = null) do={:log warning ("Conditional deleting all".$dynamic." entries in address-list: $listname")}
  } else={:put "Entries not conditional deleted in address-list: $listname"}; # ENDIF ERASE
 :for x from=1 to=$partnumber step=1 do={
   # get filesize to be compared to the orignal one and if changed then retry
   :local comparesize ([/tool fetch url=$url keep-result=no as-value]-&gt;"total")
   :if ($comparesize = 0 &amp;&amp; $downsize &gt; 0) do={ :set $comparesize $downsize}
   
   # fetching the chunks from the webserver when the size of the source file has not changed
   # empty array when the source file changed. No processing is done till the next complete retry
   :if ($comparesize = $filesize) do={:set $data ([:tool fetch url=$url http-header-field="Range: bytes=$start-$end" output=user as-value]-&gt;"data")} else={:set $data [:toarray ""]; :set $retryflag true}
     #:if ($ownposix = null) do={
  # determining the used delimiter in the list, when not provided in the config
   # this only run once and so the impact on the import time is low
    :local ipv4Posix  "^[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}"
    :local ipv4rangePosix "^[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}\\.[0-9]{1,3}/[0-9]{1,2}"
    :local domainPosix  "^.+\\.[a-z.]{2,7}"
    :local sdata $data;
   # removes any lines at the top of the file that could interfere with finding the correct posix. Setting remarksign is needed
    :while ([:pick $sdata 0 1] = $remarksign) do={ :set $sdata [:pick $sdata ([:find $sdata "\n"]+1) [:len $sdata]] }    
    :while ([:len $sdata]!=0 &amp;&amp; $delimiter = null) do={ # The check on length of $sdata is for if no delimiter is found.   
       :local sline [:pick $sdata 0 [:find $sdata "\n"]]; :local slen [:len $sline];
       # set posix depending of type of data used in the list
       :if ($sline ~ $ipv4Posix)    do={:set $posix $ipv4Posix;     :set $iden "List identified as a IPv4 list"}
       :if ($sline ~ $ipv4rangePosix)do={:set $posix $ipv4rangePosix; :set $iden "List identified as a IPv4 with ranges list"}
       :if ($sline ~ $domainPosix)    do={:set $posix $domainPosix; :set $iden "List identified as a domain list"}
       :if ($sline ~ $posix) do={:put $iden}
      :if ($sline ~ $posix) do={ # only explore the line if there is a match at the start of the line.
      :do {:if ([:pick $sline 0 ($slen-$send)] ~ ($posix."\$") || $send &gt; $slen) do={
        :set $delimiter [:pick $sline ($slen-$send) ($slen-($send-1))]; :set $result true} else={:set $send ($send+1)}  
             :if ($result) do={ :set  $extra [:pick $sline ($slen-$send) ($slen-($send-1))]
              :if ( $extra = " " )   do={ :set $delimiter [:pick $sline ($slen-$send) ($slen-($send-2))] }
              :if ( $extra = "  " )  do={ :set $delimiter [:pick $sline ($slen-$send) ($slen-($send-3))] }
              :if ( $extra = "   " ) do={ :set $delimiter [:pick $sline ($slen-$send) ($slen-($send-4))] }
             }; # EndIf result
      } while (!$result); # EndDoWhile
    }; #IF sline posix
:set $sdata [:pick $sdata ([:find $sdata "\n"]+1) [:len $sdata]]; # cut off the already searched lines
:if ($delimiter != null) do={:local sdata [:toarray ""]} ; #Clearing sdata array ending the WhileDo loop
    }; #WHILE END $sdata
    :local sdata [:toarray ""]
   :if ([:len $delimiter] = 0) do={ :set $delimiter "\n"; :set $delimiterShow "New Line" } else={ :set $delimiterShow $delimiter }; # when empty use NewLine 20220529
   #} else={:put "User defind Posix: $ownposix"; :set $posix $ownposix } ; # ENDIF ownposix = null
   :if ($delimiter != null &amp;&amp; $displayed ) do={:set $displayed false; :put "Using config provided delimiter: \"$delimiterShow\""}
   :if ($posix = null) do={:set $posix "."}; # Use a match all posix if nothing is defined or found 
   :if (!retryflag) do={:put "Reading Part: $x $start - $end"}   
   :if ($timeout = null) do={:local timeout 00:00:00}; # if no timeout is defined make it a static entry.    
   # Only remove the first line only if you are not at the start of list
   
:while ( [:pick $data 0 1] = $remarksign) do={ :set $data [:pick $data ([:find $data "\n"]+1) [:len $data]] }; # removes the invalid line (Spamhaus) 
   
   :if ($start &gt; 0) do={:set $data [:pick $data ([:find $data "\n"]+1) [:len $data]]}
     :while ([:len $data]!=0) do={
       :local line [:pick $data 0 [:find $data "\n"]]; # create only once and checked twice as local variable
       :if ( $line ~ $posix &amp;&amp; $line~heirule) do={    
        :do {add list=$listname address=[:pick $data 0 [:find $data $delimiter]] comment=$comment timeout=$timeout; :set $counter ($counter + 1)} on-error={}; # on error avoids any panics        
       }; # if IP address &amp;&amp; extra filter if present
      :set $data [:pick $data ([:find $data "\n"]+1) [:len $data]]; # removes the just added IP from the data array
      # Cut of the end of the chunks by removing the last lines...very dirty but it works
      :if ([:len $data] &lt; 256) do={:set $data [:toarray ""]}    
     }; # while

  :set $start (($start-512) + $maxsize); # shifts the subquential start back by 512  
  :set $end (($end-512) + $maxsize); # shift the subquential ends back by 512 to keep the 
  }; # if retryflag
 }; #do for x
 
}; # for retry
 :if ($counter &lt; 1) do={:set $resultline "Import was NOT successfull! Check if the list $listname is still being maintained."} else={:set $resultline "Completed reading $counter items into address-list $listname." } 
 :put $resultline
 :if ($nolog = null) do={:log warning $resultline }
 :if ($counter &gt; 0) do={:do {/ip firewall address-list remove [find where list=("backup".$listname)]} on-error={} } else={
 :do {:foreach i in=[/ip firewall address-list find list=("backup".$listname)] do={/ip firewall address-list set list=$listname $i }} on-error={}
 :put "Restoring backup list: $listname" 
 :if ($nolog = null) do={:log warning "Restoring backup list: $listname"}
 }; # if counter restore on failure and remove on success
}; # do
$update url=https://antifilter.network/download/ipsmart.lst listname=vpn-ip timeout=1d nolog=1
$update url=https://antifilter.network/download/subnet.lst listname=vpn-subnet timeout=1d nolog=1
}

# To be used configline settings:
# url=        https://name.of.the.list
# listname=name of address-list

# Optinal settings
# timeout=the time the entry should be active. If omited then static entries are created.
# comment=puts this comment on every line in the choosen address-list (default: no comment)
# heirule=this will select on a word on each line if to import or not (default: no heirule)
# noerase=any value, then the current list is not erased (default: erase)
# ownPosix=allow to enter a onw regEX posix to be used (not ative at this moment)
# nolog=        any value, then don't write to the log (default: writing to log)
```

Будем использовать списки ipsmart и subnet. Для каждого списка свой address-list. Если будете использовать другие списки, то задавайте их в `$update url`.

Помещаем его в **System - Scripts - Add new**. Сам скрипт вставляется в поле source.  
Policy: read, write, test.

![](%D0%A2%D0%BE%D1%87%D0%B5%D1%87%D0%BD%D1%8B%D0%B9%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%BD%D0%B0%20Mikrotik%20BGP%20%D0%B8%20Address%20lists%20+%20Mangle.%20%D0%9A%D0%B0%D0%BA%20%D1%80%D0%B5%D0%B0%D0%BB%D0%B8%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D1%8C%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%20%D0%BF%D0%BE%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%B0%D0%BC%20%20%D0%A5%D0%B0%D0%B1%D1%80/shmqlhxowdeksdiffvvxbouybvm.png)

Сразу запустим **Run Script**.

Проверить, что адреса добавляются можно в **Firewall - Address Lists**.

Добавим в крон **System - Scheduler - Add new**.

-   Policy такие же, как у скрипта
    
-   Interval - раз во сколько времени. Например, раз в 23 часа: 23:00:00.
    
-   On Event - имя скрипта, которое задали при создании скрипта.
    

Одной командой:

```
/system scheduler
add interval=23h name=ScheduleGetLists on-event=GetLists policy=\
    read,write,test
```

Создаём таблицу, маршрут в туннель для этой таблицы и два правила маркировки для двух списков. Нужно подставить вместо $INTERFACE имя вашего VPN интерфейса.

```
/routing table
add disabled=no fib name=vpn
/ip route
add comment=vpn disabled=no distance=1 dst-address=0.0.0.0/0 gateway=$INTERFACE pref-src="" routing-table=vpn suppress-hw-offload=no
/ip firewall mangle
add action=mark-routing chain=prerouting disabled=no dst-address-list=vpn-ip new-routing-mark=vpn passthrough=yes
/ip firewall mangle
add action=mark-routing chain=prerouting disabled=no dst-address-list=vpn-subnet new-routing-mark=vpn passthrough=yes
```

Делайте трассировку с клиента, маршрут должен проходить через туннель.

### Как выключить обход блокировок при mangle+address-list?

Просто выключить mangle правило. **ip - firewall - mangle**, кнопка Disable для созданного выше правила.

## Список доменов в Address lists

Переходим к самому интересному. Я достал Mikrotik из шкафа именного для этого. Встроенный в ROS резолвинг доменов и добавление их IP-адресов в списки реализован просто добавлением в address-lists нужного домена.

Добавляйте домен, он сразу резолвится и появляется в этом списке. Звучит неплохо, но есть парочка проблем.

Это хорошо работает на 5, 10, 20 доменах, но если число доменов переваливает примерно за сотню, то начинаются тормоза. Это может быть как просто от большого списка без настроенного правила mangle, ну а с ним роутеру точно будет плохо.

Возьмём список с ~500 доменами и с помощью sed сформируем команды для добавления этих доменов в address-list.

Предупреждаю, вашему роутеру, скорее всего, станет плохо. Если хотите повторить, не делайте это на проде.

```
wget -qO- https://raw.githubusercontent.com/itdoginfo/allow-domains/main/Russia/inside-raw.lst | sed "s/.*/add address=&amp; list=vpn-domains/"
```

Перед этим списком добавим команду

```
/ip/firewall/address-list/
```

Я протестировал это пару десятков раз и результат добавления 500 доменов бывает разным. Тестировал ROS с дефолтными настройками.

Бывает, что сразу всё виснет, включая консоль. А бывает, что на первый взгляд всё окей, но чуть потыкав, замечал большие лаги. И после зачистки списка всё встаёт на свои места.

Если вам потребуется удалить все записи во всех списках

```
/ip firewall address-list remove [find]
```

Или удалить все записи только в одном списке. Пример с vpn-domains

```
/ip firewall address-list remove [find list~"vpn-domains"]
```

При попытке удаления списков часто выдаёт ошибку `no such item (4)`. Бывает нужно повторить несколько раз, а бывает, помогает только рестарт роутера.

Сначала я подумал: "у меня просто роутер почти самой младшей модели".

По моим опытам это не зависит от мощности железа. Я потестировал на виртуальной машине c ROS, дав ей 4 Ryzen 5 5600U ядра и гигабайт оперативной памяти. Закинув туда те же 500 доменов. Результат был такой же.

Но я заметил, что на формирование самого списка IP-адресов из доменов влияет DNS сервер, который использует роутер. На некоторых DNS-серверах список формировался со скрипом, на некоторых быстрее. Видимо, тут дело в лимитах на запросы. Или может дело в ограчении 1Мбит/с для ROS Cloud без лицензии.

Но помимо моих экспериментов, один человек поделился своим опытом: hAP ac2 при сотне доменов роутер нагревался и сам рестартился.

Если не хотите экспериментировать со своим роутером, я показываю, что происходит в этом случае на видео, ссылка вначале.

Могу предположить, что просто такова реализация. В официальной документации не нашёл рекомендаций по количеству доменов в списках.

Я не понимаю, почему про это никто не пишет. Вообще не увидел нигде упоминаний, что будет с парой сотней доменов в address-lists. К этому же можно прийти, решая проблему с суммаризацией.

Помимо этого, есть ещё парочка проблем:

-   Он умеет резолвить только субдомены, это ставит в выгодное положение на фоне того же Dnsmasq (работает **только** по wildcard). В случае когда основного домена не должно быть в списках, а субдомен нужен. Но, с другой стороны, это проблема, вам нужно задать все субдомены ресурса. Для маленьких ресурсов несложно собрать, а для больших, у которых они могут быть динамическими и зависят от локации запроса, это проблема.
    
-   ROS сама резолвит эти домены. Во-первых, мы тут не можем сделать wildcard и встречается с первой проблемой, во-вторых, ROS опирается на TTL DNS-записи. В случае если TTL 8 часов, запись будет обновляться раз в 8 часов, а бывает, TTL ставят около нуля, и роутер будет постоянно обновлять записи. И это никак не настраивается.
    

Даже если бы не было проблем с формированием списка IP-адресов и mangle правило каким-то чудом работало бы быстро, то всё равно упираешься в проблемы описанные выше.

Таким образом, использовать большое количество доменов в Address list плохая затея.

### Как пользоваться резолвингом в ROS

Что можно взять из встроенного функционала резолвинга ROS?

Добавить в Address-lists самые необходимые домены. Скорее даже ресурсы, которых нет в предоставляемых списках подсетей или они резолвятся у вас по-другому.

Остановиться, когда роутеру станет плохо.

В случае, если уже используется обход по Address List, добавить ещё один magle

```
/ip firewall mangle
add action=mark-routing chain=prerouting disabled=yes dst-address-list=vpn-domains new-routing-mark=vpn passthrough=yes
```

В случае использования BGP, то же самое что для других списков, только подставьте своё имя списка в $LIST:

```
/routing table
add disabled=no fib name=vpn
/ip route
add comment=vpn disabled=no distance=1 dst-address=0.0.0.0/0 gateway=$INTERFACE pref-src="" routing-table=vpn suppress-hw-offload=no
/ip firewall mangle
add action=mark-routing chain=prerouting disabled=no dst-address-list=$LIST new-routing-mark=vpn passthrough=yes
```

Таким образом, лучшее решение для Mikrotik, к которому я пришёл - это использовать BGP + mangle по доменам.

### Костыли

Костыли основаны на том, что вы используйте DNS-сервер не ROS, а поднятый где-то у себя. Он, тем или иным образом, добавляет IP-адреса в Address-list или routes через BGP по списку доменов.

Вот для примера статья про добавление через BGP: [Обход блокировок РКН с помощью DNSTap и BGP](https://habr.com/ru/articles/467547/)

Что ещё можно придумать?

-   OpenWrt как виртуальная машина на роутере с DNS сервером и каким-то скриптом внутри
    
-   Можно поставить DNS сервер в docker на роутере, если ваш роутер имеет ARM архитектуру
    
-   Можно по USB подключить какой-нибудь zero PI и развернуть это на нём.
    

Я ничего из этого не реализовывал. Если вдруг вы, да, будет интересно почитать про ваш опыт.

## Список доменов в DNS static, wildcard. Добавлено в ROS 7.5

Благодаря Юрию Ряполову [@xen85](https://habr.com/users/xen85), [стало известно](https://t.me/itdogcomments/1086) ещё об одном способе добавления IP-адресов доменов в address-list. Большое спасибо ему за это открытие.

В версии 7.5 [было добавлена](https://mikrotik.com/download/changelogs) следующая фича

```
*) dns - added "address-list" parameter for static DNS entries (CLI only);
*) dns - added "match-subdomain" option for static entries (CLI only);
```

Не обращайте внимание на `CLI only`, в последующих версиях это появилось и в веб-интерфейсе и в winbox.  
Это реализация по подобию Dnsmasq+ipset.

Добавляем домен в /ip/dns/static

```
/ip dns static add name=terraform.io type=FWD forward-to=8.8.8.8 address-list=vpn-domains match-subdomain=yes
```

Теперь IP-адреса домена и его субдоменов при резолвинге будут складываться в address-list vpn-domains.  
В **type** должен быть FWD и обязательно к какому DNS-серверу перенаправлять запрос. Параметр **match-subdomain** добавляет IP-адреса субдоменов указанного домена в address-list.

IP-адреса в address-list будет жить, пока живёт запись в /ip/dns/cache, а там она живёт, пока не истечёт её TTL.  
Mikrotik hAP ac lite нормально вывез 555 доменов в static. Нагрузки на роутер при этом не добавилось.

Тут есть проблема, что обязательно надо куда-то перенаправлять DNS запрос. Нельзя просто без перенаправления складывать в address-list. Ещё нельзя использовать [вместе с DoH](https://wiki.mikrotik.com/wiki/Manual:IP/DNS):

> Currently DoH is not compatible with FWD type static entries, in order to utilize FWD entries, DoH must not be configured.

Таким образом, если ваш провайдер блокирует/подменяет DNS-запросы можно:

-   Перенаправлять DNS-запросы в туннель. Нужен настроенный резолвер на IP-адресе гейта VPN-сервера или в его подсети.
    
-   Использовать отдельный DNS-сервер, который развёрнут рядом с роутером.
    

Если кто-то это автоматизирует, дайте знать, добавлю ваш код сюда.

Без автоматизации можно сформировать список. Пример для добавления:

```
wget -qO- https://raw.githubusercontent.com/itdoginfo/allow-domains/main/Russia/inside-raw.lst | sed "s/.*/\/ip dns static add name=&amp; type=FWD forward-to=8.8.8.8 address-list=vpn-domains match-subdomain=yes/"
```

Удаления:

```
wget -qO- https://raw.githubusercontent.com/itdoginfo/allow-domains/main/Russia/inside-raw.lst | sed "s/.*/\/ip dns static remove [\/ip dns static find name=&amp;]/"
```

Подставьте свой DNS-сервер вместо 8.8.8.8.

Дальше все запросы к зарезолвенным IP-адресам направляются в туннель также с помощью mangle по примеру выше.

В Dnsmasq такая фича [была добавлена](https://thekelleys.org.uk/dnsmasq/CHANGELOG) 10 лет назад в версии 2.66. Будем надеется, что разработчики ROS сделают ещё один type, который будет обращаться к локальному резолверу без forwarding. И тогда будет совсем хорошо.

## Провайдер вмешивается в DNS трафик

В ROS7 есть поддержка DoH, настраивается в **IP - DNS**. Необходимо просто указать DoH сервер. Например, `https://1.1.1.1/dns-query`.

## Блокировка VPN-протоколов и Mikrotik

Mikrotik не поддерживает комбайны XRay и Sing-box. Поэтому в этом случае вижу два с половиной пути решения:

1.  Если вы организация, можете предоставить IP-адреса ваших VPN-серверов куда-то там и забыть о такой проблеме.
    
2.  Цепочка серверов. Как мы знаем, VPN-протоколы не блокируются, если пакет идёт к российскому IP-адресу, поэтому можно поднять WG на российском сервере и уже с него перенаправлять трафик до сервера с нероссийским IP-адресом. Либо вообще использовать для этих целей сервер рядом с Mikrotik, который также будет на своей стороне перенаправлять трафик в туннель, который маскируется под HTTPS.
    
3.  Мне кто-то писал про разворачивание XRay/Sing-box в docker прям на Mikrotik. Опять же, нужен роутер с ARM процессором. Для меня это выглядит странно и ненадёжно, но может, я ошибаюсь.
    

С другой стороны, есть вероятность, что вам это не понадобится в ближайшем году. Или покрайней мере хватит SSTP, пока системно не научатся его блочить.

## Релевантные материалы

-   [Статья по настройке BGP на Mikrotik от автора antifilter.download](https://habr.com/ru/articles/549282/)
    
-   [Статья по настройке BGP для OpenWrt](https://habr.com/ru/articles/743572/)
    

___

Хотите использовать большие списки доменов и Shadowsocks/VLESS/etc прямо на роутере? Рекомендую посмотреть в сторону реализации этого на [OpenWrt](https://habr.com/ru/articles/767464/).

Если у вас есть какой-то опыт по любому из пунктов статьи - будет интересно почитать. Особенно про списки доменов.

Материалы про VPN и точечный обход блокировок, которые не дотягивают до полноценной статьи, публикую в [моём телеграм-канале](https://t.me/+soFXzmlolyxlYjEy).
