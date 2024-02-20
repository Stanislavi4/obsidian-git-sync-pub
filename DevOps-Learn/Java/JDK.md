Содержание

1. 1. [Что такое JDK](https://blog.skillfactory.ru/glossary/jdk/#что-такое-jdk)
2. 2. [Три технологических пакета Java](https://blog.skillfactory.ru/glossary/jdk/#три-технологических-пакета-java)
3. 3. [Как устроен JDK и что в него входит](https://blog.skillfactory.ru/glossary/jdk/#как-устроен-jdk-и-что-в-него-входит)
4. 4. [Что такое пакеты Java Development Kit](https://blog.skillfactory.ru/glossary/jdk/#что-такое-пакеты-java-development-kit)
5. 5. [Версии Oracle JDK и OpenJDK](https://blog.skillfactory.ru/glossary/jdk/#версии-oracle-jdk-и-openjdk)
6. 6. [Сборки JDK](https://blog.skillfactory.ru/glossary/jdk/#сборки-jdk)
7. 7. [Что нужно для начала работы](https://blog.skillfactory.ru/glossary/jdk/#что-нужно-для-начала-работы)
8. 8. [Как установить JDK](https://blog.skillfactory.ru/glossary/jdk/#как-установить-jdk)

JDK, или Java Development Kit — это набор инструментов для разработки на языке [Java](https://blog.skillfactory.ru/java-komu-i-dlya-chego-nuzhen/). В него входят все компоненты, необходимые для того, чтобы программировать на этом языке. Он позволяет компилировать, то есть «собирать», и запускать написанный код.

## **Что такое JDK**

Java Development Kit разработан компанией Oracle, но сейчас существуют дистрибутивы, созданные сторонними разработчиками. О них мы подробнее расскажем ниже. Версия от Oracle — до сих пор одна из основных.

JDK работает на всех популярных операционных систем: Windows, [Linux](https://blog.skillfactory.ru/glossary/linux/), macOS и других. Он бесплатный, его можно легко [скачать](https://www.oracle.com/java/technologies/downloads/) с официального сайта и установить в систему.

JDK пользуются все Java-разработчики. Он нужен, чтобы писать программы на Java, создавать виртуальное окружение, запускать их и отлаживать. Без JDK можно писать код, но его не получится «собрать» в программу или запустить. Установленный JDK — обязательное условие для программирования на Java, без него работать с языком не получится.

## **Три технологических пакета Java**

В Java есть три технологии для разработки программ. Это сам JDK, JRE и JVM. Определение JDK мы уже дали, теперь поговорим об остальных.

- **JRE —** Java Runtime Environment. Это набор инструментов для запуска программы. Без них код не будет работать и исполняться. JRE — составная часть JDK и входит в пакет по умолчанию.
- **JVM —** Java Virtual Machine. Особенность Java в том, что для запуска программ язык создает виртуальную среду, «оболочку», внутри которой все выполняется. Благодаря этой особенности язык — кроссплатформенный. Код может без доработок выполняться практически на любом устройстве. JVM — набор технологий, который реализует виртуальную оболочку. JVM входит в пакет JRE, точно так же как сам JRE входит в JDK.

Все три технологии реализованы внутри Java Development Kit. Достаточно скачать и установить JDK, чтобы пользоваться любой из трех: разрабатывать программы, создавать виртуальные оболочки и запускать код внутри них.

## **Как устроен JDK и что в него входит**

Слово kit означает «набор», и JDK — это именно набор программного обеспечения. Разные версии пакетов могут различаться по функциональности, а сам разработчик может при установке выбрать, что хочет загрузить, а что нет. Но стандартная сборка JDK обычно включает все компоненты, перечисленные ниже.

**JRE.** Про JRE мы рассказали выше. Он входит в JDK, потому что нужен для запуска программ, а запуск — неотъемлемая часть разработки.

**Компилятор.** Компиляция — это «сборка» программы, перевод ее в исполняемые коды. Компилятор — ПО, которое принимает файл с кодом и преобразует его в исполняемый вид. Компиляторы существуют для всех компилируемых языков. Без сборки программа не сможет исполниться, потому что до перевода в исполняемый код она представляет собой обычный текст — компьютер воспринимает ее именно так.

Для большинства языков существует несколько компиляторов. Java не исключение, но чаще всего для него используют оптимизирующий компилятор Javac.

**API.** [API](https://blog.skillfactory.ru/glossary/api/) — это набор инструментов и интерфейсов для связи какой-либо технологии с другими. Java API входят в JDK, потому что могут понадобиться при разработке.

**Библиотека классов.** Чтобы писать код, необходимы стандартные классы. Библиотека стандартных классов тоже есть в JDK. При установке можно выбрать вариант с исходными кодами. Они дают возможность просматривать и изменять изначальный код стандартных библиотек. Но менять стандартные классы разработчикам обычно не советуют.

**Отладчик.** Это набор инструментов для отладки программ, написанных на Java. Отлаживать код без этих инструментов бывает практически невозможно. Они дают возможность, например, изолировать выполнение определенного участка кода, выполнять программу пошагово с остановкой после каждой строки или останавливать ее на определенной точке с просмотром логов и значений переменных.

**Другие инструменты.** Набор ПО в пакете называется Java Development Tools: в нем находятся и отладчик, и API, и другие программные компоненты для разработки. Также это могут быть разнообразные утилиты для облегчения работы с языком, документация и примеры кода.

## **Что такое пакеты Java Development Kit**

У JDK есть три вариации: SE, EE и ME. Они различаются назначением.

**JDK SE.** Это Standard Edition, стандартная версия, которой пользуется большинство разработчиков. Если вы собираетесь учиться Java, мы советуем выбирать именно ее. Для обучения и решения большинства задач этой версии достаточно. А если ее вдруг перестанет хватать, вы всегда сможете переустановить JDK в другой вариации.

**JDK EE.** Это Enterprise Edition, версия для корпоративной разработки. Кроме стандартных инструментов в нее входят дополнительные, которые облегчают создание энтерпрайз-приложений. Начинающим разработчикам EE может понадобиться, если они зхотят писать программы для веба. Технологии для создания веб-приложений, такие как сервлеты, включены именно в EE.

**JDK ME.** Это Micro Edition или Mobile Edition, версия для разработки под мобильные приложения. В нее входят все инструменты SE и дополнительные возможности для устройств, ограниченных в памяти и мощности. Телефоны и планшеты не такие мощные, как ПК, поэтому программы должны быть легкие и гибкие. Java ME помогает сделать их именно такими.

## **Версии Oracle JDK и OpenJDK**

**Разделение**. Изначально OpenJDK был проектом по созданию свободного JDK — версии с открытым исходным кодом. Когда-то JDK был один и принадлежал компании Sun Microsystems, которая теперь называется Oracle. Он был проприетарным — бесплатным, но с закрытым исходным кодом. Потом компания объявила о своем намерении сделать JDK открытым, и с тех пор развитие набора разделилось на две версии. Вместо одного JDK стало существовать два:

- **Oracle JDK,** по-прежнему принадлежащий Oracle, более стабильный, но менее активно развивающийся. Не весь его код открытый;
- **OpenJDK,** полностью открытая версия, которая основана на Oracle, но поддерживается и разрабатывается в том числе силами сообщества. Она более гибкая и активно развивается, но иногда бывает менее стабильной.

**Отличия.** Сначала после разделения версии почти ничем не отличались, потому что были основаны на одном и том же изначальном JDK. Но со временем различий стало больше, потому что OpenJDK дорабатывало сообщество, а Oracle JDK — компания Oracle. Главное различие — по-прежнему открытость или закрытость компонентов.

## **Сборки JDK**

Обычно в разговорах о JDK подразумевают набор инструментов от компании Oracle или OpenJDK. Они основные, но не единственные. Существуют сборки на основе стандартного JDK, созданные и опубликованные другими компаниями, обычно на базе OpenJDK. У них та же функциональность, но с дополнительными компонентами и услугами. Например, существует Liberica JDK от компании BellSoft — у нее российская техподдержка, а еще она оптимизирована под микросервисы.

Некоторые варианты JDK позволяют выбрать наполнение. Например, при скачивании можно указать, какая JVM вам нужна — их существует несколько, у каждой свои особенности. Такая возможность, например, есть у AdoptOpenJDK, еще одной популярной сборки набора ПО для разработчиков. Она распространяется под свободной лицензией.

## **Что нужно для начала работы**

Функциональности JDK достаточно, чтобы начать разрабатывать проекты на Java. Но код придется писать в обычных текстовых редакторах вроде «Блокнота»: специальная среда в набор не входит. Использовать текстовые редакторы не всегда удобно, особенно для больших проектов, поэтому обычно код пишут с помощью IDE — интегрированных сред разработки. Это программы, в которых есть редактор кода, связь со средствами разработки и дополнительные возможности, например установщик сторонних библиотек.

Мы рекомендуем пользоваться IDE при работе с языками программирования. Это проще и удобнее, чем писать в текстовых редакторах и компилировать через консоль: все можно сделать внутри среды, даже запустить код и посмотреть на его работу. Кроме того, в большинство IDE для Java по умолчанию входит JDK, так что установить и настроить инструменты будет легче. Популярные IDE для Java — [Intellij IDEA](https://blog.skillfactory.ru/glossary/intellij-idea/), Eclipse, NetBeans и другие.

## **Как установить JDK**

Выберите версию JDK: Oracle JDK, OpenJDK или другую. Скачайте нужное наполнение — для старта это обычно SE. Процесс установки может различаться для разных операционных систем, но в целом вариантов два:

- скачивается архив с нужной функциональностью, который распаковывается и устанавливается с помощью специальных программ. Для архива надо сразу, еще перед скачиванием указать, какое наполнение JDK вы хотите;
- скачивается приложение-установщик, исполняемый файл, который устанавливает JDK. Установщик спрашивает, какие компоненты вы хотите установить: для начала подойдет стандартный набор, потом вы сможете при желании изменить наполнение.

Для Windows также нужно настроить переменные среды через «Панель управления».

После установки и настройки можно начинать работу. Для этого требуется IDE или обычный текстовый файл.