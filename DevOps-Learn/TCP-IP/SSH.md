Содержание

1. 1. [Где используется SSH](https://blog.skillfactory.ru/glossary/ssh/#где-используется-ssh)
2. 2. [Как работать по SSH](https://blog.skillfactory.ru/glossary/ssh/#как-работать-по-ssh)
3. 3. [SSH-сервер](https://blog.skillfactory.ru/glossary/ssh/#sshсервер)
4. 4. [SSH-клиент](https://blog.skillfactory.ru/glossary/ssh/#sshклиент)
5. 5. [Туннелирование в SSH](https://blog.skillfactory.ru/glossary/ssh/#туннелирование-в-ssh)

SSH (Secure Shell — «безопасная оболочка») — это сетевой протокол для удаленного управления операционной системой с помощью командной строки и передачи данных в зашифрованном виде.

## **Где используется SSH**

SSH-протокол применяется:

- в удаленном системном администрировании локальных сетей;
- в управлении работой почтовых служб (защите данных);
- для скрытой передачи файлов большого размера внутри сети;
- для переноса проектов между серверами хостинг-провайдеров;
- для подключения в сетевых многопользовательских играх и т. д.

## Как работать по SSH

Чтобы установить SSH-соединение, необходимы два компонента: SSH-сервер и SSH-клиент. Сервер прослушивает определенный порт (по умолчанию это порт 22) и при успешной аутентификации дает доступ пользователю. Все команды, которые используются на SSH-клиенте, отправляются через защищенный канал связи на SSH-сервер, на котором они выполняются и откуда отправляют результат работы обратно клиенту.

![[6a88cd0c1970ff06de260fbcc2a4e9a5_MD5.png]]

Схема работы SSH

## SSH-сервер

Устанавливается на управляемой операционной системе и принимает входящие подключения от клиентских машин, проверяя соединение одним из способов:

- по IP-адресу клиента, что не слишком безопасно из-за риска подмены;
- по публичному ключу клиента и имени пользователя. Нужно создать приватный (закрытый) и публичный (открытый) ключ. Зашифровав информацию одним ключом, можно расшифровать ее только другим;
- по паролю клиента, который передается в зашифрованном виде. Это один из наиболее распространенных вариантов. Вводить его нужно при каждом подключении.

Платные и бесплатные SSH-серверы есть для всех распространенных ОС:

- BSD — OpenSSH;
- Linux — dropbear, lsh-server, openssh-server;
- Windows — freeSSHd, copssh, WinSSHD, OpenSSH и т. д.

## SSH-клиент

Используется для входа на удаленный сервер и выполнения различных команд. Через клиент выполняется управление удаленным компьютером, в том числе:

- управление файлами и директориями;
- просмотр и редактирование файлов;
- контроль рабочих процессов;
- управление архивами, базами данных MySQL и т. д.

==SSH-клиенты разработаны для всех десктопных и мобильных ОС.== Имеют платные и бесплатные версии:

- для Linux/BSD — openssh-client, putty, ssh, Vinagre;
- MS Windows — PuTTY, SecureCRT, ShellGuard;
- Android — connectBot;
- Linux и MacOS имеют встроенный SSH-клиент, дополнительная настройка не требуется.

Встроенный SSH-клиент Windows, основанный на OpenSSH, предустановлен в ОС, начиная с Windows 10 1809. Узнать, как установить его для более ранних версий Windows, можно в [инструкции](https://www.putty.org/).

## Туннелирование в SSH

Чтобы использовать SSH безопасно, создается SSH-туннель. Это означает, что данные шифруются на одном конце SSH-соединения и расшифровываются на другом.

Для защиты данных нужно соблюдать правила:

- запретить удаленный root-доступ;
- выбрать нестандартный порт для SSH-сервера;запретить подключаться с пустым паролем или отключать вход по паролю;
- использовать длинные SSH2 RSA-ключи;
- ограничить количество IP-адресов, с которых возможен доступ;
- следить за сообщениями об ошибках аутентификации;
- запретить доступ с опасных адресов;
- установить системы для обнаружения вторжений (IDS — Intrusion Detection System).
- использовать ловушки. Например, Tarpit — это порт-ловушка для замедления входящих соединений. Если сторонняя система подключается к порту, быстро закрыть соединение будет невозможно. Она будет расходовать системные ресурсы и ждать, когда соединение прервется по тайм-ауту или будет разорвано вручную.

Хотя SSH — надежный протокол удаленного управления, он также имеет уязвимости. Следует постоянно помнить о том, что никакая защита не является совершенной, а лучшая из них — это внимательность и аккуратность пользователя.