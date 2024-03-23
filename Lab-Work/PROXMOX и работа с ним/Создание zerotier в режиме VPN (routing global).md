### Установите ZeroTier[](https://docs.zerotier.com/bridging#install-zerotier "Прямая ссылка для установки ZeroTier")

https://www.ZeroTier.com/download/

```
curl -s https://install.zerotier.com | sudo bash






### Присоединяйтесь к сети ZeroTier[](https://docs.zerotier.com/bridging#join-zerotier-network "Прямая ссылка для подключения к сети ZeroTier")

```
sudo zerotier-cli join $NETWORK_ID
sudo zerotier-cli listnetworks - проверяем что сеть ок