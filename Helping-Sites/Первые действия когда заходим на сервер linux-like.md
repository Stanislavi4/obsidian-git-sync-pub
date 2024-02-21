# Обзор сервера (обязательно при входе в ВМ)
```
hostname - что важно ввести команду "hostname" при входе на сервер, чтобы узнать его имя. (топчик!)
```

```
pwd - Он также рекомендует проверить текущую папку с помощью команды "pwd". (rm и rv действия приведут последствия) (топчик!)
```

```
df -i - иноды на диске при заполнении 100% паника смотрим iuse
```

```
cat /etc/os-release - показывает, как узнать операционную систему сервера, версию ядра и текущую дату. (топчик!)
```

```
uname -r - показывает ядро сборки линукса (топчик!)
```

```
date - время показывает, некоторые сервисы ломаются, если сбивается время
```

```
timedatectl status - аналог времени, только показывает детально (NTP, СИНХРОНИЗАЦИЯ) (топчик!)
```



```
htop, top - просмотр нагрузки на систему, и процессоров (топчик!)
```
# Осмотр сервера
```
sudo apt install sysstat  - набор системных утилит.  ls семейство утилит работает только при установке пака; (два раза таб для подробнсоти) (топчик!)
```

```
`dstat` - проверка в секунду какие нагрузки происходят (dstat --top-cpu --top-io --top-latency --top-mem)
```

```
cd /var/log - где записываются логи (топчик!)
```

```
ls -la - просмотр файлов и их прав (топчик!)
```

```
sudo journalctl - лог всей системы 
```

```
sudo journalctl  -u <файл лога> - в хорошем читаемости (топчик!)
```

```
sudo journalctl  _PID=<берем из htop>
```

```
uptime - проверка всего работы сервака, сколько нагружен
```

```
 cat /proc/meminfo - подробно что чем забита память
```
# Детально о процессах

```
ps aux - показывает все процессы
kill <PID> - убиваем процесс
```

```
systemctl status <имя сервиса> - смотрим состояние процесса
```

```
`strace -p <PID>` - посмотреть системные вызовы
```
# Место на дисках и Состояние дисков
```
df -hi - проверка дисков, занятое место и т.п. (80% паникуем) (топчик!)
stat <артефакт>  - утилита для просмотра папки/файла
```

```
lsblk --fs - улучшений ввид дисков 
```

```
sudo apt install smartmontools
smartctl  -a  <диск sda> - смарт диска
```

```
cat /proc/mdstat - просмотр состояние рейд массива
```

```
ls -lah - просмотр и прав и размера диска (топчик!)
```

```
`sudo du -hd1  <папку>` - смотрим размер папки, содержимое его
```

```
sudo apt install ncdu - в граф виде (gui) просмотр места файлов по каталогам
```



# Сеть
```
`ip a` - сетевые устрйоства, мк-адреса, ip-адреса
```

```
ip r - маршрутизация и метрики
```
# Мониторинг сети
```
`traceroute <адрес> -nA -q2 `- проверка пинга, и траблуштинг трассировки 
```

```
cat /etc/resolv.conf - показывает имя сервера DNS 
```

```
`sudo ss -tunlp | -grep <порт>` - поиск отрытых портов
```
# Проверка сети
```
`mtr  <адрес>` - в реальном времени диагностика на потери пакетов
```

```
dig @8.8.8.8 <имя локального сервера> - nslookup аналог проверка ответов dns записей
```

```
dig -t mx <имя локального сервера> - запрос почтовой записи
```
опционально
```
tcpdump -i any port <порт> -nn  - аналог wireshark
```

```
`sudo install iperf3` - проверяем соединение между двумя тачками
```

```
sudo iperf3 -s  -режим сервера (на одной тачке локальной)
```

```
sudo iperf3 -c <адрес тачки сервера> --verbose -t 10
```


# Логи системы и сервисов

```
tail -f -n50 /var/log/syslog  - смотрим все изменения последние 50
ls  -lah /var/log/auth.log - аутификационные события
ls -lah /var/log/kern.log - ядровые события
dmesg -T - логи для людей
journalctl -xeu <имя сервиса> - логи определенного сервиса
```

# Работа с пользователями

```
sudo useradd --shell path/to/shell <никнейм> - создание пользователя с указанием выбора интерпретатора (bash, sh, zsh)
sudo passwd <никнейм> - задаём пароль пользовователю
sudo usermod -aG <group><никнейм> - задаём права пользователю и группу прав

sudo --list - показывает список всех доступных пакетов и привилегий (!RWE), которые можно выполнить с помощью `sudo`. Определения того, какие действия вы можете выполнять с помощью суперпользовательских прав.

id -u <username> - показывает информацию о пользователе, включая его идентификатор (UID), имя пользователя (user name) и группу, к которой он принадлежит.Для определения UID или username для конкретного пользователя.
```

```
sudo --list - смотрим права у твоего пользователя
sudo deluser <user> sudo - удаляем права
su root - заходим под рут
adduser <user> sudo - добавляем права пользователю
```


```
chmod 777 - права на файл. Абсолютно всем группам и юзерам права на всё 
chmod x -  права только для владельца файла (read, write, execute) и группы (execute only). Другие не имеют прав на этот файл или диреторию.
chown -R <юзер> path/to/directory -  используется для изменения владельца и группы файлов или директорий. (-R всю директорию и содержимое)
```

###### *Прелюдие установки openssh Win*

```
# Install the OpenSSH Client
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

# Install the OpenSSH Server
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0

# Start the sshd service 
Start-Service sshd 
# OPTIONAL but recommended: 
Set-Service -Name sshd -StartupType 'Automatic' 
# Confirm the Firewall rule is configured. It should be created automatically by setup. Run the following to verify
if (!(Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled)) { Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..." New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22 } else { Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' has been created and exists." }
```
# Прочее

```
Создание SSH ключа с Win на Linux
ssh-keygen -t ed25519 -f "$HOME\.ssh\id_ed25519-remote-ssh"
scp C:\Users\<виндовыйюзер>\.ssh\id_ed25519-remote-ssh.pub <уд.юзер>@(hostIP_or_DNS):/home/<уд.юзер>/.ssh/authorized_keys - утилита копирует данные по ssh(22). Чтоб на удаленных тачках есть ssh
```


```
curl - запросы к различным ендпоинтам (сильная команда) (http/https)
curl -Lv <DNS сайта/ip сайта> - запрос полный с командами ошибок API (200/300/400/500)
curl -v telnet://127.0.0.1:22 - проверка открытие порта
curl cheat.sh/<утилита> - полный гайд о утилите
```

```
wget
```

```
touch
```

```
tmux
```

```
find . -name "*.csv"
grep "имя чего хотим найти в файле" <название файла> | column -t -s, | less -s
```


```
awk -  язык обработки строк (синтаксис языка С ). Аналог grep
```

```
ssh-keygen -t  ed25519 -C "admin11" -f ~/.ssh/id_rsa
```

```
man -h <имя утилиты> - шпаргалка никудышная по всем командам
```