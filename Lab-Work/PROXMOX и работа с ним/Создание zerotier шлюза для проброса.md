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