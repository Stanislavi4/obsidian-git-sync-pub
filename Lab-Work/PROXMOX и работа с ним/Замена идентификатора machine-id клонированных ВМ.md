При клонировании виртуальных машин с ОС Linux может получиться так, что после клонирования, системы-клоны будут иметь повторяющийся идентификатор machine-id. Хотя подразумевается, что эти идентификаторы у разных компьютеров должны быть уникальны и могут использоваться разным ПО для определения идентичности той или иной Linux-системы. Эти идентификаторы присваиваются системе на этапе установки ОС и сохраняются в конфигурационном файле /etc/machine-id

Проверить идентификаторы на разных системах можно просто заглянув в файл /etc/machine-id или с помощью утилиты hostnamectl:

```
root@SERVER1:~# hostnamectl | grep "Machine ID"
        Machine ID: 193a81762be145a68c133e071890df59

root@SERVER2:~# hostnamectl | grep "Machine ID"
        Machine ID: 193a81762be145a68c133e071890df59

```
Чтобы сгенерировать новый machine-id для клонированной виртуальной системы, можно воспользоваться утилитой systemd-machine-id-setup

Порядок действий будет такой:

- Изменяем права доступа к файлу /etc/machine-id, так ка по умолчанию он доступен только на чтение
    
- Очищаем содержимое файла /etc/machine-id
    
- С помощью утилиты systemd-machine-id-setup генерируем новый идентификатор в /etc/machine-id
    
- Возвращаем исходные права доступа на файл /etc/machine-id
    

```
# chmod 644 /etc/machine-id
# > /etc/machine-id
# systemd-machine-id-setup
Initializing machine ID from D-Bus machine ID.
# chmod 444 /etc/machine-id
# cat /etc/machine-id
5f3ff6fda7e5a7091651473e648ff962
```


ЛИБО

## Устранение неполадок[](https://technotim.live/posts/cloud-init-cloud-image/#troubleshooting)

Если вам нужно сбросить идентификатор вашего компьютера

```
sudo rm -f /etc/machine-id
sudo rm -f /var/lib/dbus/machine-id

```
Затем выключите его и не загружайте. Новый идентификатор будет сгенерирован при следующей загрузке.Если этого не произойдет, вы можете запустить:

```
sudo systemd-machine-id-setup
```