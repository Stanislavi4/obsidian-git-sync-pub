Основа ubuntu 22.04
#### Примерный план[](https://docs.zerotier.com/bridging#an-example-plan "Прямая ссылка на пример плана")

| Имя                                          | Значение                                | Упоминается ниже как |
| -------------------------------------------- | --------------------------------------- | -------------------- |
| Подсеть физической локальной сети            | 192.168.192.0/24                        |                      |
| ДИАПАЗОН DHCP физической локальной СЕТИ      | С 192.168.192.65 по 192.168.192.126     |                      |
| Диапазон автоматического назначения ZeroTier | С 192.168.192.129 по 192.168.192.190    | $ZT_POOL             |
| Управляемый маршрут ZeroTier                 | 192.168.192.0/23                        | $ZT_ROUTE            |
| IP-адрес шлюза по умолчанию                  | 192.168.192.1                           | $GW_ADDR             |
| IP-адрес моста                               | 192.168.192.2/24 (или используйте DHCP) | $BR_ADDR             |
| Идентификатор сети ZeroTier                  | d5e04297a19bbd70                        | $NETWORK_ID          |
| Название сетевого интерфейса ZeroTier        | zt3jnwghuq                              | $ZT_IF               |
Устанавливаем:

### Обновите операционную систему[](https://docs.zerotier.com/bridging#update-the-operating-system "Прямая ссылка для обновления операционной системы")

```
sudo apt update && sudo apt -y full-upgrade && sudo reboot
```

После завершения работы снова войдите в систему

### Установите ZeroTier[](https://docs.zerotier.com/bridging#install-zerotier "Прямая ссылка для установки ZeroTier")

https://www.ZeroTier.com/download/

```
curl -s https://install.zerotier.com | sudo bash
```

### Теперь давайте установим некоторые переменные оболочки[](https://docs.zerotier.com/bridging#lets-set-some-shell-variables-now "Прямая ссылка на Давайте установим некоторые переменные оболочки прямо сейчас")

```
NETWORK_ID=<your-network-id>BR_IF="br0"BR_ADDR=<your-bridge-address>GW_ADDR=<your-gateway-address>
```

### Присоединяйтесь к сети ZeroTier[](https://docs.zerotier.com/bridging#join-zerotier-network "Прямая ссылка для подключения к сети ZeroTier")

```
sudo zerotier-cli join $NETWORK_ID
sudo zerotier-cli listnetworks - проверяем что сеть ок
```

### Настройте устройство в ZeroTier Central[](https://docs.zerotier.com/bridging#configure-the-device-in-zerotier-central "Прямая ссылка для настройки устройства в ZeroTier Central")

- Перейдите в раздел Участников сети
- Откройте значок гаечного ключа для получения дополнительных настроек и проверьте
- Авторизовать участника
- ### Настройте управляемый маршрут ZeroTier[](https://docs.zerotier.com/route-between-phys-and-virt#configure-the-zerotier-managed-route "Прямая ссылка для настройки управляемого маршрута ZeroTier")

В `my.zerotier.com/network/$NETWORK_ID`->`Settings`->`Managed Routes`

Это добавляет еще один маршрут к каждому устройству, подключенному к сети ZeroTier.

|Пункт назначения|(Через)|
|---|---|
|$PHY_SUB|$ZT_ADDR|

Например:

|Пункт назначения|(Через)|
|---|---|
|192.168.100.0/23|172.27.0.1|

Настройте маршрут назначения как немного **больший**, чем фактическая физическая подсеть, здесь `/23` вместо `/24` (меньшее число означает большую подсеть в этом обозначении) Это приводит к тому, что устройства, находящиеся как в физической, так и в сети ZeroTier, предпочитают физическое подключение.


### Включить переадресацию IP[](https://docs.zerotier.com/route-between-phys-and-virt#enable-ip-forwarding "Прямая ссылка для включения переадресации IP-адресов")

Это может варьироваться в зависимости от дистрибутива Linux. Обычно:

Отредактируйте`/etc/sysctl.conf`, чтобы раскомментировать `net.ipv4.ip_forward`. Это позволяет перенаправлять данные при загрузке.

Чтобы включить это сейчас

```
sudo sysctl -w net.ipv4.ip_forward=1
```

### Настройка iptables[](https://docs.zerotier.com/route-between-phys-and-virt#configure-iptables "Прямая ссылка для настройки iptables")

Назначьте некоторые переменные оболочки (персонализируйте их)

```
PHY_IFACE=eth0; ZT_IFACE=zt7nnig26
```

Добавление правил в iptables

```
sudo iptables -t nat -A POSTROUTING -o $PHY_IFACE -j MASQUERADEsudo iptables -A FORWARD -i $PHY_IFACE -o $ZT_IFACE -m state --state RELATED,ESTABLISHED -j ACCEPTsudo iptables -A FORWARD -i $ZT_IFACE -o $PHY_IFACE -j ACCEPT
```

Сохраните правила iptables для следующей загрузки

```
sudo apt install iptables-persistentsudo bash -c iptables-save > /etc/iptables/rules.v4
```

### Тест[](https://docs.zerotier.com/route-between-phys-and-virt#test "Прямая ссылка на тест")

- Отключите Wi-Fi на своем телефоне
- Подключите его к сети zerotier, авторизуйте его
- Попробуйте получить доступ к чему-либо в физической локальной сети