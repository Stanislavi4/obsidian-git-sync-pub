Содержание

1. 1. [Особенности JavaScript](https://blog.skillfactory.ru/glossary/javascript/#особенности-javascript)
2. 2. [Где применяется JavaScript](https://blog.skillfactory.ru/glossary/javascript/#где-применяется-javascript)
3. 3. [Для чего нужен JS](https://blog.skillfactory.ru/glossary/javascript/#для-чего-нужен-js)
4. 4. [Как работает JavaScript в браузере](https://blog.skillfactory.ru/glossary/javascript/#как-работает-javascript-в-браузере)
5. 5. [Недостатки JavaScript](https://blog.skillfactory.ru/glossary/javascript/#недостатки-javascript)
6. 6. [Стоит ли учить JavaScript?](https://blog.skillfactory.ru/glossary/javascript/#стоит-ли-учить-javascript)
7. 7. [Что не получится сделать на JavaScript](https://blog.skillfactory.ru/glossary/javascript/#что-не-получится-сделать-на-javascript)

JavaScript — это интерпретируемый язык программирования, который используют для написания frontend- и backend-частей сайтов, а также мобильных приложений. Часто в текстах и обучающих материалах название языка сокращают до JS. Это язык программирования высокого уровня, то есть код на нем понятный и хорошо читается.

JS поддерживают все популярные браузеры. Во frontend-части сайтов язык используют для создания интерактива (анимаций, всплывающих форм, автозаполнения), так как он связан с [HTML](https://blog.skillfactory.ru/glossary/html/) и [CSS](https://blog.skillfactory.ru/glossary/css/) и может ими манипулировать. В backend-части с языком JavaScript работают на платформе Node.js. С ее помощью, например, разрабатывают серверные веб-приложения и подключают библиотеки. В поисковике Google на JavaScript работает строка автозаполнения, а Netflix, Uber, eBay используют его в своем backend. Уже 6 лет JS — [самый популярный](https://incrussia.ru/news/yazyki-programmirovaniya/) язык среди разработчиков по версии [GitHub](https://blog.skillfactory.ru/glossary/github-2/).

## Особенности JavaScript

JavaScript простыми словами называют языком скриптов или сценариев. Скрипты — это набор инструкций, которые выполняются при загрузке страницы. Браузер самостоятельно интерпретирует код на JavaScript, для этого даже не требуется компиляция (перевод языка программирования в машинный код).

Скрипты можно прописать внутри кода страницы или подключить к HTML отдельным файлом. Например в стандартной разметке index.html JS-код прописывают внутри тега script, помещенного в тег body:

<!DOCTYPE html>

<html lang="en">

<head>

<meta charset="UTF-8">

<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Документ</title>

</head>

<body>

<h1>Привет!</h1>

<script>

console.log('Привет, мир!');

</script>

</body>

</html>

Тогда в браузере благодаря инструкции console.log появится слово «Привет!».

![[4c38c37f05f8acde239ace1b902d14aa_MD5.png]]

Но обычно для этого создается отдельный файл с расширением .js. Его называют script.js и прописывают код в нем:

console.log(‘Hello world!’)

![[f7cdc7d7f8870a2bef52ae6e4d697fa5_MD5.png]]

А в основном коде уже внутри тега script прописывают путь к этому файлу:

<body>

<h1>Привет!</h1>

<script src="js/script.js"></script>

</body>

</html>

JS — мультипарадигменный язык, так как он поддерживает разные парадигмы (стили) программирования и особенности:

- **Объектно-ориентированный** — представляет программу как систему отношений между объектами — наборами данных и функций.
- **Императивный** — в этом стиле программирования используют инструкции, которые выполняются последовательно, а для доступа к данным используются переменные, значение которых может быть переписано в ходе вычисления.
- **Функциональный** — в нем вычисление основано на математических функциях. Так как функциональное программирование не опирается на переменные, которые могут менять значение, результаты запросов будут всегда одинаковыми.
- **Динамическая типизация**: Переменные в JavaScript не требуют объявления типа данных. Они могут изменять свой тип в процессе выполнения, что делает код более гибким, но также требует внимательного управления типами.
- **Обработка событий**: JavaScript является мощным инструментом для обработки событий на веб-страницах. Он может реагировать на действия пользователей, такие как клики мышью, нажатия клавиш и другие.
- **Манипуляция DOM**: JavaScript может изменять структуру и содержимое веб-страницы, обеспечивая динамическое обновление контента без перезагрузки страницы.
- **Асинхронные операции**: JavaScript позволяет выполнять асинхронные операции, такие как загрузка данных с сервера, без блокирования выполнения других задач.
- **Серверный JavaScript**: С помощью платформы Node.js JavaScript может быть использован на серверной стороне для создания высокопроизводительных и масштабируемых веб-приложений.
- **Множество фреймворков и библиотек**: JavaScript имеет обширное сообщество разработчиков и большое количество библиотек и фреймворков, таких как React, Angular, Vue.js, и другие, что упрощает создание сложных веб-приложений.
- **Кроссплатформенность**: JavaScript может быть использован для разработки веб-приложений на различных платформах, включая веб, мобильные устройства и даже десктоп.
- **Открытый стандарт**: JavaScript базируется на открытых стандартах ECMAScript, что обеспечивает его интероперабельность и расширяемость.

Веб-страницы частично обрабатываются с помощью JavaScript на компьютере пользователя. Это снижает нагрузку на сервер: часть операций выполняется без запросов к нему, что экономит время и трафик.

## Где применяется JavaScript

- **Клиентская** **часть веб****—****приложений** **(frontend).** Это интерфейс страницы, то есть всё, что видит пользователь: контент, кнопки, формы обратной связи, меню. С помощью JS интерфейс реагирует на действия пользователя (клики мыши, нажатия клавиш), также язык отвечает за запоминание данных и автозаполнение форм.
- **Серверная** **часть веб****—****приложений** **(backend).** Серверный код пишут на платформе Node.js. На JS работают, например, запросы AJAX (asynchronous javascript and XML), которые отправляются на сервер в фоновом режиме, без перезагрузки веб-страницы, и push-уведомления — всплывающие сообщения в браузере, которые реализуются с помощью технологии Comet. Такие уведомления приходят со специального Comet-сервера, который постоянно поддерживает соединение с браузером. Как раз с помощью JavaScript устанавливается это соединение.
- **Мобильные** **приложения** **на Android, iOS, Windows Mobile** — когда нужно кросс-платформенное приложение или адаптация веб-приложения, а языков [Kotlin](https://blog.skillfactory.ru/glossary/kotlin/) (для Android) и Swift (для iOS) недостаточно, то используется JavaScript.
## Для чего нужен JS

В веб-приложениях HTML отвечает за разметку страницы. Расположение контента, внешний вид интерфейса описывается с помощью CSS, а JavaScript отвечает за интерактивность и функционал этого интерфейса. Вот примеры задач, которые решает JS:

- **Взаимодействие с** **пользователем****.** Всплывающие окна с полем для ввода почты или других данных, уведомления об использовании cookie.
- **Обработка** **данных** **HTML.** JS помогает заполнить необходимые поля в формах, проверять буквенные и числовые значения, а также управлять HTML-элементами на странице. Например, в конструкторе лендингов [Tilda](https://tilda.cc/ru/) можно перетаскивать блоки и по клику менять шрифты, цвета текста, фонов и другие значения.
- **Анимация.** Это не только движущиеся объекты на странице и кнопки, но и карусель в галерее или плавная прокрутка страницы. Например, [Pinterest](https://www.pinterest.ru/) с помощью JavaScript создал эффект бесконечной ленты.
- **Математические вычисления.** С помощью JS-скриптов на сайтах реализованы калькуляторы, например подсчет суммы заказа в корзине интернет-магазина.
- **Мобильные приложения**: С использованием фреймворков, таких как React Native и NativeScript, JavaScript может быть использован для создания мобильных приложений для разных платформ.
- **Серверный JavaScript**: используется на серверной стороне с использованием платформы Node.js для создания высокопроизводительных и масштабируемых веб-приложений.
- **Обработка событий**: JavaScript используется для обработки событий, таких как клики мышью, нажатия клавиш, отправка форм и другие действия пользователя. Это позволяет создавать реактивные и отзывчивые интерфейсы.

## Как работает JavaScript в браузере

Действия пользователя на странице вызывают события: клик на кнопке запускает анимацию, захват мышкой и перемещение курсора двигают объект по странице. Каждое из этих действий выполняется с помощью скриптов, написанных на JavaScript. У них есть определенный алгоритм работы:

1. Пользователь совершает действие — нажимает на кнопку «Зарегистрироваться».
2. Браузер фиксирует выполнение определенных условий — клик мыши в конкретной области на странице. За реакцию на действие отвечает команда onclick, которая запускает прописанный JS-код.
3. Затем запускается JS-код, который прописан для этих условий, — анимация нажатия кнопки и открытие формы регистрации.
4. Страница полностью или частично обновляется — в зависимости от настроек форма может открыться в этой же или в соседней вкладке.

## Недостатки JavaScript

Несмотря на множество преимуществ, JavaScript также имеет некоторые недостатки:

- **Браузерные несовместимости**: Разные браузеры могут по-разному интерпретировать и поддерживать JavaScript. Это может вызвать проблемы с кросс-браузерной совместимостью и потребовать дополнительного кода для обработки различий.
- **Безопасность**: JavaScript выполняется на стороне клиента, что делает его уязвимым для атак, таких как внедрение вредоносного кода (XSS). Разработчики должны быть осторожными и следовать лучшим практикам безопасности.
- **Производительность**: Несмотря на улучшения в исполнении JavaScript-кода, он может быть менее эффективным по сравнению с некоторыми другими языками, особенно для вычислительно сложных задач.
- **Глобальная область видимости**: JavaScript имеет одну глобальную область видимости, что может вызвать конфликты имен переменных между разными частями кода.
- **Callback Hell**: Вложенные колбэки в асинхронном коде могут создавать так называемое «Callback Hell» — сложные и труднопонимаемые структуры кода.
- **Долгая история развития**: JavaScript имеет долгую историю развития, что означает наличие устаревших конструкций и несовместимостей между разными версиями языка.
- **Отсутствие строгой типизации**: Для некоторых разработчиков динамическая типизация JavaScript может быть источником ошибок и затруднить отладку.
- **Ограниченный доступ к ресурсам компьютера**: Из соображений безопасности JavaScript имеет ограниченный доступ к ресурсам компьютера пользователя, что может затруднить выполнение некоторых задач.
- **Зависимость от интернета**: Большинство веб-приложений на JavaScript требуют постоянного соединения с интернетом, и они могут быть менее функциональными в оффлайн-режиме.
- **Сложность асинхронного кода**: Работа с асинхронными операциями и обещаниями (Promises) может быть сложной и вызвать проблемы, такие как утечки памяти.

## Стоит ли учить JavaScript?

JavaScript остается самым популярным языком программирования: по статистике, [97% сайтов](https://en.wikipedia.org/wiki/Programming_languages_used_in_most_popular_websites) написано именно на нем. ==JavaScript — один из языков программирования, которые просто освоить без опыта.== Он сравнительно несложный, а ошибки легко обнаружить. Например, если кнопка при нажатии не работает или работает неверно, то нужно искать ошибку в коде. Чтобы кодить на JS, нужно понимание, как устроены HTML и CSS. Когда освоите язык, сможете лучше разбираться с работой библиотек и фреймворков (React, jQuery, Angular, Lodash, Node.js, Vue.js и других).

В среднем JS-разработчики [зарабатывают](https://russia.trud.com/salary/692/78054.html) 84 839 рублей в месяц, а самые высокие зарплаты в России по данным [hh.ru](https://hh.ru/search/vacancy?area=1&clusters=true&enable_snippets=true&ored_clusters=true&text=Javascript&order_by=salary_desc) составляют около 300 тыс. рублей.

## Что не получится сделать на JavaScript

Вот какие ограничения есть у JS:

- JS не умеет читать, записывать или запускать файлы напрямую с устройства.
- Чтобы связать всплывающее окно с основной страницей, придется писать дополнительный код, иначе они не смогут обмениваться введенными данными.
- JS взаимодействует только с тем сервером, с которого открыта страница. Для работы со сторонними сайтами и сервисами придется прописать дополнительный код с обеих сторон, чтобы они могли взаимодействовать друг с другом.