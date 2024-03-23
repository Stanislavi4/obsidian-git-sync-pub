
### Установите ZeroTier[](https://docs.zerotier.com/bridging#install-zerotier "Прямая ссылка для установки ZeroTier")

https://www.ZeroTier.com/download/

```
curl -s https://install.zerotier.com | sudo bash

```


### Присоединяйтесь к сети ZeroTier[](https://docs.zerotier.com/bridging#join-zerotier-network "Прямая ссылка для подключения к сети ZeroTier")

```
sudo zerotier-cli join $NETWORK_ID
sudo zerotier-cli listnetworks - проверяем что сеть ок
```

Для того чтобы сервер мог выступать в качестве VPN узлам нам необходимо включить на сервере NAT и перенаправление пакетов. Чтобы проверить активировано ли у вас перенаправление портов необходимо выполнить следующую команду:

```
sudo sysctl net.ipv4.ip_forward
```

Если в выводе команды вы получите `net.ipv4.ip_forward = 0` это означает что перенаправление пакетов между сетевыми интерфейсами у вас выключен и для его активации необходимо отредактировать файл **/etc/sysctl.conf** в котором нужно добавить или раскомментировать строку

```
net.ipv4.ip_forward = 1
```

После сохранения изменения в файле выполняем следующую команду

```
sudo sysctl -p
```

Чтобы наш сервер позволял выполнят переадресацию между интерфейсами нам необходимо добавить несколько правил в файрвол.

Сперва узнаем имя ZeroTier интерфейса для этого выполняем команду

```
ip link show
```

В выводе мы увидим все сетевые интерфейсы которые доступные в системе

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether ff:ff:ff:ff:ff:ff brd ff:ff:ff:ff:ff:ff
3: zt2lrt5nfx: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 2800 qdisc fq_codel state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether ff:ff:ff:ff:ff:ff brd ff:ff:ff:ff:ff:ff
```

Нас интересует сетевой интерфейс который начинается на zt — это и есть интерфейс ZeroTier, в примере выше это zt2lrt5nfx.

Теперь добавим правила в iptables. Первое правило добавляет преобразование сетевых адресов и масТеперь добавим правила в iptables. Первое правило включает NAT на нашем сервере

```
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

Вторым правилом разрешаем перенаправление трафика и отслеживание активных интерфейсов

```
sudo iptables -A FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
```

И последнее правило разрешает перенаправление трафика с интерфейса ZeroTier на главный сетевой интерфейс

```
sudo iptables -A FORWARD -i zt2lrt5nfx -o eth0 -j ACCEPT
```

Все внесенные правила iptables после перезагрузки будут удалены и для того чтобы их сохранить нам необходимо будет установить дополнительный пакет **iptables-persistent**

```
sudo apt-get install iptables-persistent
```

```
sudo netfilter-persistent save
```

Для того чтобы проверить все активные правила iptables можно выполнить команду

```
sudo iptables-save
```

На этом настройка на стороне сервера завершена. Нам остается настроить правила маршрутизации в панели ZeroTier. Переходим по адресу [https://my.zerotier.com/network](https://my.zerotier.com/network) и в блоке **Managed Routes** добавляем новый маршрут

![[8f02379d1cf3f1eadc676cf1e0546853_MD5.png]]

В **Add Routes** вписываем 0.0.0.0/0, а в **(Via)** внутренний IP адрес сервера который вы настраивали для работы в качестве VPN в данном примере это 10.144.164.26.

По-умолчанию правила маршрутизации не применяются при подключении к сети и чтобы они начали действовать их необходимо активировать на стороне клиента.

#### Настройка клиента в Linux с Default Route

В файл /etc/sysctl.conf необходимо внести следующую строку

```
sudo /etc/sysctl.conf
```

```
net.ipv4.conf.all.rp_filter=1
```

Сохраним файл и запустим команду которая применит внесенные изменения.

```
sudo sysctl -p
```

И теперь для того чтобы подключить наш сервер к сети ZireTier с применением маршрутов выполняем команду заменив в ней NetworkID на ваш ID сети.

```
sudo zerotier-cli set NetworkID allowDefault=1
```

#### Настройка остальных клиентов ZeroTier с Default Route

Чтобы активировать Default Route в клиентах которые установлены в Windows, macOS, Android и iOS необходимо в настройках подключения включить опцию Allow Default Route.