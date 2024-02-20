Содержание

1. 1. [Для чего используют Sass](https://blog.skillfactory.ru/glossary/sass/#для-чего-используют-sass)
2. 2. [Как работает Sass](https://blog.skillfactory.ru/glossary/sass/#как-работает-sass)
3. 3. [Установка Sass](https://blog.skillfactory.ru/glossary/sass/#установка-sass)
4. 4. [Переменные в Sass](https://blog.skillfactory.ru/glossary/sass/#переменные-в-sass)
5. 5. [Использование !global](https://blog.skillfactory.ru/glossary/sass/#использование-global)

Sass — это метаязык (язык для описания другого языка), который упрощает и ускоряет написание CSS-кода. Его часто называют препроцессором [CSS](https://blog.skillfactory.ru/glossary/css/) — это означает, что Sass имеет свой синтаксис, из которого генерирует CSS-код, понятный любому браузеру.

## Для чего используют Sass

Препроцессор Sass помогает:

- сделать CSS-код понятнее и проще. Его легче масштабировать, обновлять и поддерживать;
- расширить функциональность. С помощью Sass можно использовать CSS-константы, встроенные функции, вложенные правила, примеси (смешанные стили), наследование и так далее;
- избежать многократного повторения одинаковых фрагментов кода. Это экономит время разработчика, уменьшает объем файлов стилей и ускоряет обработку страниц.


Разберем использование Sass на примере. В цветовой палитре сайта преобладают три цвета:

![[3853946e3268da0766f050bc5fbace4f_MD5.png]]

Пример основы цветовой палитры

Без использования препроцессора разработчику нужно вручную вводить HEX-значения каждого из этих оттенков несколько раз. Sass решает проблему просто:

/* определяем переменные для основных цветов */

_$primary_1: #a2b9bc;  
$primary_2: #b2ad7f;  
$primary_3: #878f99;_

/* используем переменные в стилях*/

_.main-header {  
background-color: $primary_1;  
}  
.menu-left  
background-color: $primary_2;  
}  
.menu-right {  
background-color: $primary_3;  
}_

Если дизайнер (или разработчик) решит изменить цвет какого-либо элемента, достаточно поменять значение один раз — у переменной. 

## Как работает Sass

Отличительная особенность Sass — наличие двух синтаксисов.

**1. SASS** не использует фигурные скобки, характерные для стилей CSS. Вместо них применяются отступы. Расширение файла — **.sass.**

Пример кода:

_&:disabled  
color: $mdc-button-disabled-ink-color  
cursor: default  
pointer-events: none_

**2. SCSS** использует фигурные скобки. Расширение файла — **.scss.**

Пример кода:

_&:disabled {  
color: $mdc-button-disabled-ink-color;  
cursor: default;  
pointer-events: none;  
}  
}_

При этом оба диалекта поддерживают не только стандартные CSS-комментарии, но и построчные примечания, отделяемые от кода двойным слэшем //:

/* это пример обычного CSS-комментария */

_.main-header {  
background-color: $primary_1; // это пример построчного комментария  
}_

Браузеры не понимают код Sass — они распознают и отображают только стили CSS. Для преобразования используется специальное приложение — препроцессор, который переводит конструкции в понятный браузеру формат. В готовый CSS-код не включаются построчные комментарии. В ходе компиляции в CSS передаются только стандартные примечания, отделяемые от кода символами /* */.

## Установка Sass

Sass написан на языке Ruby и отличается минимальными системными требованиями:

- не зависит от конкретной программной платформы — пакет можно установить на компьютер с любой операционной системой;
- код CSS, полученный в результате работы препроцессора, обрабатывается любыми браузерами.

Для установки с помощью менеджера пакетов NPM достаточно выполнить команду:

_npm install -g sass_

Команда для установки в случае использования менеджера пакетов Homebrew выглядит так:

_brew install sass/sass/sass_

При использовании менеджера Chocolatey в среде Windows нужно выполнить команду:

_choco install sass_

Хороший бесплатный инструмент для работы с Sass — приложение [Scout-App](http://scout-app.io/), которое работает на Windows, Linux и MacOS.

## Переменные в Sass

Переменные применяются для хранения данных и значений для повторного использования. Sass поддерживает несколько типов переменных:

- строки;
- списки;
- числовые значения;
- пустые значения null;
- цвета;
- логические (булевы) значения.

Проиллюстрируем использование переменных на примере. На скриншотах видно, как содержимое файла .scss определяет код CSS и HTML. Используются переменные:

- **myFont —** задает шрифт Helvetica;
- **myColor —** устанавливает красный цвет;
- **myFontSize —** определяет размер шрифта, 18 кегль;
- **myWidth —** задает ширину контейнера, 680 пикселей.

Слева — переменные в файле .scss, справа — представление в браузере.

![[0f065520fb196ff3d52b3c0d47a30626_MD5.png]]

Переменные в файле .scss и браузере

Слева — скомпилированный CSS-код, понятный браузеру.

![[3abc09cc30c292d9415ffddf1640d2ba_MD5.png]]

Пример скомпилированного кода

Слева — HTML-код, в котором есть ссылка на скомпилированный файл стиля CSS.

![[67af891a40c16e33b23d6fd4ae8d56c3_MD5.png]]

HTML-код с ссылкой на файл стиля CSS

Область видимости переменных Sass ограничивается вложенными элементами — это позволяет предотвратить влияние стилей друг на друга. Зададим различные цвета для основного цвета и для заголовка:

_$myColor: red;  
h1 {  
$myColor: green;  
color: $myColor;  
}  
p {  
color: $myColor;  
}_

Может показаться, что переменная $myColor передаст зеленый цвет основному тексту. Однако Sass четко разделяет стили элементов — цвет параграфа будет красным, поскольку стили CSS в результате компиляции выглядят так:

_h1 { color: green;}p { color: red;}_

Область видимости переменных в Sass ограничена вложенными элементами.

![[54701ab66052d5457c182b77ba76659b_MD5.png]]

Область видимости переменных в Sass

## Использование !global

==По умолчанию область видимости всех переменных в Sass ограничена, но у разработчика есть возможность переопределения области видимости отдельных переменных — с локальной на глобальную.== Это возможно с помощью метода **!global.** Добавление переключателя !global станет указанием на то, что переменная должна быть видна на всех уровнях.

Для иллюстрации воспользуемся кодом из предыдущего примера: добавим к переменной **$myColor** элемента **h1** переключатель !global:

_$myColor: red;  
h1 {  
$myColor: green !global;  
color: $myColor;  
}  
p {  
color: $myColor;  
}_

Теперь и заголовок, и текст параграфа имеют один цвет — зеленый, поскольку переменная в CSS-коде стала глобальной:

_h1 {  
color: green;  
}  
p {  
color: green;  
}_

![[3abb6ff303dfe0f86479fa21390b4702_MD5.png]]

Переключатель !global переопределяет область видимости переменной

Глобальные переменные нужно определять отдельно от локальных переменных, в файле под названием _globals.scss. Он включается в основной код с помощью функции **@include.**