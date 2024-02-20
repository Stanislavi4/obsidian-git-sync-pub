Содержание

1. 1. [История](https://blog.skillfactory.ru/glossary/ftp/#история)
2. 2. [Как работает FTP](https://blog.skillfactory.ru/glossary/ftp/#как-работает-ftp)
3. 3. [Для чего нужен протокол FTP](https://blog.skillfactory.ru/glossary/ftp/#для-чего-нужен-протокол-ftp)
4. 4. [Преимущества FTP](https://blog.skillfactory.ru/glossary/ftp/#преимущества-ftp)
5. 5. [Недостатки FTP](https://blog.skillfactory.ru/glossary/ftp/#недостатки-ftp)
6. 6. [Защищенные версии протокола FTP](https://blog.skillfactory.ru/glossary/ftp/#защищенные-версии-протокола-ftp)
7. 7. [Распространенные FTP-клиенты](https://blog.skillfactory.ru/glossary/ftp/#распространенные-ftpклиенты)

FTP (File Transfer Protocol) — протокол удаленной передачи данных через локальную или глобальную компьютерную сеть. С его помощью пользователь может подключиться к FTP-серверу для просмотра содержимого каталогов, а также чтобы загрузить или скачать файлы. ФТП был разработан еще в 1971 году и сегодня относится к числу базовых протоколов передачи данных, которые используются в локальных и глобальных компьютерных сетях. Так как его базовая версия имеет проблемы с защищенностью, сегодня используются преимущественно более совершенные FTPS или SFTP.

## **История**

Первая версия протокола FTP появилась в 1971 году, еще до возникновения интернета как такового, но уже в эпоху первых компьютерных сетей. Структура отправляемого по нему сообщения включала следующие элементы:

- заголовок, включающий запрос к FTP-серверу (или ответ от него), а также тип и размер передаваемых данных;
- сами данные, которыми могли быть путь и имя файла, сами файлы или их списки в каталоге.

В первой версии и команды, и данные передавались по одному каналу. Из-за ограничений пропускной способности сетей того времени это делало передачу информации неудобной, к тому же доступной для перехвата. Уже через год протокол был полностью переделан: теперь команды, содержащие параметры от пользователя или сервера, передавались по одному каналу, а сами данные — по второму. Это существенно увеличило и пропускную способность, и безопасность соединения.

В последующих редакциях протокола были введены следующие возможности:

- пассивный режим работы;
- команды получения информации;
- создание, удаление и смена каталога;
- сохранение файлов под уникальными именами.

Версия FTP 1980 года получила интеграцию с TCP — пакетным протоколом передачи данных. Последняя редакция FTP была выпущена в 1987 году. Однако в последующие годы выпускались дополнения — например, включающие шифрование обоих каналов и интернациональную кодировку команд (UTF-8). Несмотря на то что FTP был разработан еще в доинтернетную эпоху, его современные и более защищенные версии широко используются и поныне.

## **Как работает FTP**

Этот протокол подразумевает двустороннее соединение, то есть передача данных осуществляется между:

- **клиентом.** Это специальная программа, установленная на устройстве — персональном компьютере или мобильном гаджете. С помощью нее пользователь отправляет запрос серверу или получает от него ответ, просматривает каталоги файлов, скачивает или загружает данные;
- **FTP-сервером.** Это тоже программное приложение, которое устанавливается на обычный ПК или рабочую станцию. С его помощью можно пересылать файлы с одного устройства на другое. FTP-сервер управляет доступом к данным по логину и паролю, обеспечивает качество передачи информации и устраняет ошибки, контролирует соединение в пассивном режиме.

Если два пользователя хотят обменяться информацией (сообщениями, файлами) по FTP-доступу, то этот процесс выглядит следующим образом:

- сначала один пользователь со своего клиента вызывает сервер, дожидается его ответа и отправляет данные;
- сервер сохраняет эти данные и параметры доступа к нему — они могут быть заданы как пользователем, так и автоматически самим клиентом (в FTP наличие логина и пароля для доступа к информации обязательно), а также устанавливает двустороннее соединение;
- второй пользователь с помощью своего клиента вызывает сервер, сообщает ему свой логин и пароль и получает доступ к сохраненной информации, которую может просмотреть или скачать.

FTP-сервер может работать в двух режимах:

- **активном.** В этом режиме клиент подключается к порту 21 на сервере и сообщает свои учетные данные и номер своего порта. В ответ FTP-сервер соединяется через этот номер с клиентом с помощью номера TCP-порта 20, устанавливая канал для передачи данных. Проблема в том, что брандмауэр компьютера может расценить ответный запрос сервера как попытку несанкционированного доступа и заблокировать соединение;
- **пассивном.** В этом режиме клиент также посылает запрос серверу со своими учетными данными, получая в ответ от него номер TCP-порта. Далее уже сам пользователь через клиент устанавливает соединение. То есть в пассивном режиме никакого ответного запроса от FTP-сервера не происходит — соответственно, на него не реагирует брандмауэр компьютера.

## **Для чего нужен протокол FTP**

Для взаимодействия в компьютерных сетях используется несколько протоколов. Например, HTTP позволяет просматривать страницы в интернете, XMPP — обмениваться мгновенными сообщениями, SMTP — отправлять и принимать электронную почту. Протокол FTP служил для обмена файлами уже в то время, когда интернета, каким мы его знаем сейчас, не существовало. Отсюда возникает вопрос: почему FTP используется до сих пор? Причин несколько:

- Для соединения по протоколу FTP не нужен полноценный веб-интерфейс, то есть сайт. Соответственно, нет необходимости запускать веб-сервер, создавать шаблоны файловых списков и использовать отдельное приложение, отдающее эти файлы, вроде WordPress. В FTP взаимодействие организовано как доступ к удаленной папке: пользователь с помощью клиента сразу видит данные и имеет возможность их скачивать без посредников. В веб-интерфейсе используется отдельная программа, отображающая файловую систему и устанавливающая ссылки на файлы.
- В FTP-протоколе уже имеются встроенные настройки авторизации и прав доступа. В веб-интерфейсе их нужно задавать отдельно, устанавливая тот же WordPress с «прикрученными» к нему плагинами управления системой доступа. Или настраивать Apache с генерацией ключей, раскладкой конфигурационных файлов по папкам. То есть в FTP все это организованно более элегантно для конечного пользователя.
- В FTP можно свободно управлять правами доступа — например, разрешить или запретить конкретным пользователям закачивать данные на сервер. В веб-интерфейсе этот процесс устроен намного сложнее.
## **Преимущества FTP**

- Возможность передавать, скачивать, просматривать и изменять на домашнем или рабочем компьютере файлы любого типа: аудио, видео, изображения, текст и т.д.
- Функция удаленного управления файлами и папками, позволяющая пользователю как скачать их на свое устройство, так и загрузить на удаленный FTP-сервер.
- Большое количество бесплатных FTP-клиентов с простым и интуитивно понятным интерфейсом.
- Дополнительные возможности протокола, среди которых прерывание, добавление, удаление, редактирование файлов, создание, изменение и удаление каталогов.
- Надежность передачи данных при условии, что установлено устойчивое соединение клиента с сервером.

## **Недостатки FTP**

Самым большим недостатком этого протокола является незащищенность канала передачи данных. В то время, когда протокол создавался, считалось, что перехватить передаваемые по нему данные технически невозможно, поэтому встроенная защита в нем отсутствует. Иными словами, сегодня по FTP все данные передаются открыто, в том числе файлы, логины и пароли. Очевидно, что в современных условиях это абсолютно небезопасно: любой сторонний пользователь может подключиться к Wi-Fi-трафику или локальной сети и получить свободный доступ к информации.

Несмотря на то, что для доступа к данным в FTP нужно предоставить серверу логин и пароль, сама система авторизации остается незащищенной. То есть злоумышленник при желании может просто подобрать распространенные логины и пароли, чтобы получить доступ к данным. К тому же сейчас такой подбор делается не медленно и вручную, а с помощью генераторов ключей, которые анализируют за секунды тысячи возможных комбинаций.

## **Защищенные версии протокола FTP**

Из-за недостаточной защищенности этого протокола «в чистом виде» его поддержка постепенно прекращается. На смену ему разрабатываются новые версии, объединяющие основные преимущества своего предшественника с повышенной защитой. Среди них можно отметить следующие.

**FTPS (FTP+TLS/SSL)** Это изначальный FTP-протокол, в котором соединение клиента с сервером защищено дополнительными протоколами SSL или TLS. Причем происходить это может явным или неявным методом:

- в первом случае зашифрованная сессия автоматически устанавливается сервером перед отправкой каких-либо данных. Однако такой способ может вызвать несогласованность между клиентом и сервером, и тогда последний обрывает соединение;
- при использовании явного метода пользователь сначала запрашивает от сервера шифрование канала передачи данных с помощью команды «AUTH TLS», а затем подтверждает способ шифрования. Если клиент этого не делает, сервер может как сохранить, так и прервать незащищенную сессию. Такой метод устраняет несогласованность между обеими сторонами.

==Внедрение в FTPS протоколов шифрования TLS и SSL позволяет зашифровать как сам канал передачи данных, так и процесс аутентификации пользователя при попытке установить соединение.==

**SFTP (Secure FTP).** Хотя название этого протокола почти идентично FTP, на деле они не связаны друг с другом. SFTP — это расширение протокола SSH. В нем передача данных идет по дополнительному каналу, установленному поверх основного защищенного соединения. То есть в этом случае сервер сначала создает основной канал, аутентифицируя клиента, и только потом запускает прикладной протокол непосредственно для передачи данных.

Помимо альтернативных протоколов со встроенной защитой, в качестве альтернативы традиционному FTP можно использовать специальные файловые сервисы. К таким относятся, например, Dropbox, имеющий встроенную функцию Dropbox Transfer для передачи данных. Здесь также имеются инструменты шифрования, управления правами доступа к файлам и другие способы защиты передаваемой информации и учетных данных. Кроме того, такие сервисы специально адаптированы под корпоративное применение, в то время как распространенные FTP-клиенты в основном используются отдельными пользователями.

## **Распространенные FTP-клиенты**

Существует большое количество программ, разработанных для взаимодействия с помощью FTP-протокола. На данный момент наиболее популярными из них являются:

- **Far Manager.** Первая версия этой программы появилась в 1996 году. Ее отличительная особенность — DOS-интерфейс, адаптированный под одноименную операционную систему (Windows в те годы только набирала популярность). Тем не менее, современные редакции прекрасно работают и на последних версиях «Виндоус». Программа поддерживает двухоконный режим, а управление осуществляется с помощью клавиатурных команд. В Far Manager также предусмотрены функции доступа к прокси, множественный поиск и замена файлов. Важное преимущество приложения — оно бесплатное.
- **Total Commander.** Это полноценный файловый менеджер с возможностью передачи данных через FTP. Первая версия выпущена еще в 1993 году, последние редакции отличаются от нее как интерфейсом, так и возможностями. Функционал Total Commander включает создание, копирование, вырезание, просмотр, редактирование, удаление и переименовывание файлов. Как и в предыдущей программе, есть поддержка двухоконного режима. Однако передача файлов происходит по незашифрованному FTP-протоколу. Кроме того, программа имеет закрытый исходный код и относится к условно-бесплатным.
- **FileZilla.** Выпущенная в 2001 году, эта программа максимально заточена под работу с FTP-протоколом, защищенным SSL, IPv6 и IDN. Поддерживает соединение с кириллическими доменами, передачу файлов размером более 4 Гб, настройку скорости передачи и загрузки данных. Интерфейс позволяет работать сразу в нескольких вкладках и оперировать данными с помощью перетаскивания мышью (технология Drag-and-Drop). На сегодняшний день это наиболее популярный FTP-клиент для ПК.

Также у пользователей устройств под управлением LINUX есть встроенный инструмент работы с FTP-соединением через командную строку. Вместе с тем производители современного системного ПО, а также прикладных программ постепенно отказываются от поддержки этого протокола в пользу более защищенных и удобных. Можно сказать, что из конечных пользователей ПК им продолжают пользоваться лишь немногие энтузиасты при организации локальной сети или для обмена файлами друг с другом. Также технология FTP (в ее защищенных версиях) до сих пор используется для передачи контента веб-сайта на хостинг.