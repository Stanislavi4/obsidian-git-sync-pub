Содержание

1. 1. [Кто пользуется Flexbox](https://blog.skillfactory.ru/glossary/flexbox/#кто-пользуется-flexbox)
2. 2. [Для чего нужен Flexbox](https://blog.skillfactory.ru/glossary/flexbox/#для-чего-нужен-flexbox)
3. 3. [Как работает Flexbox](https://blog.skillfactory.ru/glossary/flexbox/#как-работает-flexbox)
4. 4. [Какие возможности дает Flexbox](https://blog.skillfactory.ru/glossary/flexbox/#какие-возможности-дает-flexbox)
5. 5. [Для каких элементов обычно используют Flexbox](https://blog.skillfactory.ru/glossary/flexbox/#для-каких-элементов-обычно-используют-flexbox)
6. 6. [Преимущества Flexbox](https://blog.skillfactory.ru/glossary/flexbox/#преимущества-flexbox)
7. 7. [Недостатки Flexbox](https://blog.skillfactory.ru/glossary/flexbox/#недостатки-flexbox)
8. 8. [Как начать пользоваться Flexbox](https://blog.skillfactory.ru/glossary/flexbox/#как-начать-пользоваться-flexbox)

Flexbox (или просто flex) — это способ позиционирования элементов в [CSS](https://blog.skillfactory.ru/glossary/css/). С помощью этой функции можно быстро и легко описывать, как будет располагаться тот или иной блок на веб-странице. Элементы выстраиваются по заданной оси и автоматически распределяются согласно настройкам.

В [верстке](https://blog.skillfactory.ru/glossary/verstka/) есть такое понятие, как сетка, или лэйаут (layout). Это то, как блоки — крупные и мелкие — расположены на странице. Списки, наборы ссылок, виджеты и статьи — все это распределено по веб-странице не хаотично, а по определенной структуре. Эта структура и есть сетка. Flex — один из способов ее задать.

Flexbox появился в CSS 3.0. До него для построения сеток использовали таблицы и float’ы — команды для смещения элементов в сторону. Это было неудобно, трудоемко и сильно ограничивало возможности верстки. Благодаря Flexbox верстать стало удобнее и легче, появилась возможность реализовать более интересные дизайнерские идеи.

Flexbox расшифровывается как Flexible Box — гибкий ящик или блок. Гибкость — основная идея этого способа. Читается как «флексбокс» или просто «флекс».

## **Кто пользуется Flexbox**

- **Верстальщики** — позиционирование элементов с помощью языка стилей CSS относится к верстке, и в некоторых компаниях этим занимаются отдельные сотрудники.
- **Фронтенд-разработчики** — другой подход в коммерческой разработке подразумевает, что версткой занимаются фронтендеры и они же «оживляют» интерфейсы с помощью [JavaScript](https://blog.skillfactory.ru/glossary/javascript/).
- **Фуллстак-разработчики** — им тоже следует понимать, как работает верстка, хотя сталкиваются они с ней реже, чем предыдущие два специалиста.
- **Другие сотрудники** — эпизодические задачи по верстке могут появляться у специалистов техподдержки, контент-менеджеров и прочих специалистов. Но они сталкиваются с такой необходимостью редко.
## **Для чего нужен Flexbox**

Flexbox — один из двух современных методов построения сеток страницы. Сетка — своеобразный каркас, который описывает, как расположены элементы. Благодаря ей веб-страницы выглядят понятно и аккуратно, а блоки не «плывут» вразнобой. Флексы используют для построения больших и малых сеток.

- Большие, основные сетки — это те, которые описывают положение крупных блоков. Например, это заголовки, текстовые блоки, боковые блоки со ссылками и виджетами и так далее.
- Малые, микросетки — это сетки внутри крупных блоков. Они описывают, как в них располагаются более маленькие элементы: ссылки, тексты, картинки и так далее.

Еще флексбокс применяется для других задач: выравнивания элементов, расположения их в списке, мелких дизайнерских исправлений и так далее.

## **Как работает Flexbox**

Принцип работы флексбокса — выстраивание элементов внутри какого-то крупного блока по прямой оси. Распределение и поведение этих элементов описывает верстальщик.

**Создание контейнера.** Крупный блок, внутри которого расположены элементы, называется флекс-контейнером. Чтобы превратить блок во флекс-контейнер, нужно задать для него CSS-свойство:

_display: flex;_

Эту команду можно прочитать как «способ отображения: флексбокс». После этого все, что находится внутри контейнера, будет подчиняться правилам флекса — превратится во флекс-элементы. Но это касается только прямых потомков — блоков непосредственно в контейнере. Если внутри них тоже вложены какие-то элементы, на них правила не распространятся.

Например, есть список из карточек, внутри каждой из них — текст и картинка. Если список превратить во флекс-контейнер, сами карточки — элементы списка — станут флекс-элементами и автоматически распределятся по оси. Но текст и картинка внутри каждой карточки не станут флекс-элементами — для этого пришлось бы превращать во флекс-контейнер саму карточку.

**Распределение по оси.** Когда блок превращается во флекс-контейнер, его содержимое автоматически выстроится вдоль прямой оси. По умолчанию она горизонтальная и «смотрит» слева направо, но это поведение можно изменить с помощью CSS.

Ось можно представить как линию графика, а элементы — как точки на ней. Это упрощает понимание.

==Можно изменять расположение элементов на оси, но привязка к ней все равно останется. Это ключевая особенность флексбокса.==

**Отсутствие строгих параметров.** Еще одно важное свойство флекса — чтобы определить положение элементов, не обязательно строго задавать их координаты. Флексбокс автоматически заполняет все возможное пространство внутри флекс-контейнера: для этого он может сжимать или расширять элементы, изменять отступы между ними. Главное — чтобы соблюдались правила, которые задал человек.

Например, можно указать, чтобы крайние элементы в ряду прижимались к краям контейнера, а элементы между ними распределялись равномерно. Это свойство justify-content: space-between. В таком случае, каким бы ни был размер экрана пользователя, элементы будут расположены одинаково. Просто на маленьком экране пространство между ними будет меньше, а на крупном — больше. А если экран будет совсем маленьким, элементы сожмутся или перенесутся на другую строку в зависимости от того, какое поведение задал верстальщик.

Это удобно для создания «резиновой» верстки — такой, которая хорошо смотрится на экранах разных размеров. Не нужно прописывать все до пикселя для каждого возможного разрешения: многое делается автоматически. Это и есть гибкость — способность адаптироваться к разным условиям.

**Изменение параметров.** Для каждого параметра — особенностей отображения и поведения элементов, изменения оси и так далее — есть свое CSS-свойство. Нужно применить его к требуемому флекс-контейнеру или флекс-элементу — прописать это свойство с нужными значениями.

CSS дает возможность изменять свойства в зависимости от условий. Стилевой язык работает по каскаду: то, что написано ниже в коде, «перекрывает» написанное раньше. Поэтому в нижней части файла стилей можно задать другое поведение, скажем, для крупных экранов — тогда, если расширение экрана пользователя окажется большим, элементы будут вести себя иначе.

Можно, например, изменить направление оси, выравнивание элементов по ней и другие особенности. Можно и вовсе удалить флекс-контейнер: применить свойство display с другим значением, не flex. Тогда все правила, связанные с флексом, автоматически перестанут работать.

## **Какие возможности дает Flexbox**

Мы уже говорили, что поведением элементов на оси можно гибко управлять. Это причина, по которой флекс так удобен: достаточно задать набор правил, и флекс-элементы будут автоматически выстраиваться в соответствии с ним. Вот что можно сделать.

**Изменять ось.** С помощью команды flex-direction можно изменить направление оси. У команды есть четыре значения:

- row — ряд, горизонтальная линия слева направо;
- reverse-row — ряд справа налево;
- column — колонка, вертикальная линия сверху вниз;
- reverse-column — колонка снизу вверх.

Чаще всего пользуются значениями row и column. Row хорошо подходит для горизонтальных меню, списков и так далее — элементов, которые расположены «в строку». А column — для наборов элементов, расположенных вертикально, «в столбик».

**Выстраивать элементы по оси.** Когда нужное направление оси указано, элементы автоматически выстроятся вдоль него. По умолчанию они прижаты к левому или верхнему краю оси в зависимости от ее направления — горизонтального или вертикального. Но способ построения можно изменить с помощью свойства justify-content, выровнять контент.

У него есть такие значения:

- flex-start или просто start — выравнивать по «начальной точке» оси, то есть по левому краю для row или по верхнему для column;
- flex-end или end — выстраивать по «конечной точке», правой или нижней;
- center — концентрировать элементы в центре;
- space-between — прижимать крайние элементы к левому и правому краям, а элементы между ними распределять равномерно;
- space-around — то же самое, но с отступами от левого и правого края для крайних элементов. Каждый отступ от края в два раза меньше, чем отступы между элементами;
- space-evenly — то же самое, но отступы от краев такие же, как между элементами.

На практике чаще всего используют значения start, center и space-between. Последнее удобно, например, для создания горизонтальных меню — они должны тянуться по всей ширине страницы. Но тут есть нюанс, о котором мы поговорим позже.

**Выравнивать элементы.** Можно выстроить элементы не только вдоль оси, но и «поперек» нее: для row — по вертикали, для column — по горизонтали. Поведение описывается свойством align-items.

Его значения похожи на justify-content, но все же отличаются:

- stretch — элементы растягиваются поперек оси и заполняют все свободное пространство по обе стороны от нее;
- start — элементы сохраняют свой размер и выравниваются по верхнему или левому краю;
- center — элементы группируются по центру. Можно сказать, что их центральная часть «закреплена» на оси;
- end — элементы выравниваются по нижнему или правому краю;
- baseline — выравнивание происходит по «базовой линии» контента.

Помните, что в этом случае речь идет не об основном, а о вспомогательном направлении. То есть, если ось идет горизонтально (row), то свойство align-items будет распределять элементы в ряду по вертикали, и наоборот — для column.

**Изменять поведение контента и элемента.** Кроме align-items, также есть похожие свойства, но управляющие немного другими сущностями:

- order определяет порядок расположения — где будет находиться конкретный элемент;
- align-content похоже на align-items, работает только для многострочных контейнеров — там, где элементы переносятся на другую строку. Это свойство определяет расположение строк относительно контейнера. Значения у него такие же, как у justify-content;
- align-self — это align-items для отдельного элемента. Свойство задается не контейнеру, а одному из элементов внутри него. Оно «переписывает» значение align-items для конкретного элемента. Например, все остальные могут быть растянуты по stretch, а один, измененный с помощью align-self, — располагаться по центру (center).

Напрашивается идея с justify-self, но во флексбоксе такое свойство не работает — расположение каждого из элементов на оси слишком зависит от других. Зато оно есть в других способах распределения.

**Управлять переносом элементов.** Что, если элементов станет слишком много и они не поместятся на оси? В таком случае есть два варианта поведения:

- элементы автоматически сожмутся, чтобы все уместились в одном ряду или колонке;
- элементы не будут сжиматься, а излишки перенесутся на другую строку / в другую колонку.

Этим поведением управляет свойство flex-wrap. По умолчанию его значение — nowrap, то есть не переносить, а сжимать. Если указать значение wrap, элементы будут переноситься на другую строку, а wrap-reverse — будут переноситься, но в противоположном направлении. То есть новая строка возникнет не снизу, а сверху.

Какое поведение выбрать — зависит от ситуации. Иногда элементы просто не могут достаточно сжаться, и тогда при nowrap они «сломаются» и «выпадут» за пределы сетки. В таком случае лучше выбирать wrap. А иногда разработчик точно знает, что добавлять новые элементы в это место не будут, — тогда подойдет и nowrap.

**Управлять сжатием и расширением.** Выше мы сказали, что элементы внутри флекс-контейнера могут изменять размер: сжиматься и расширяться в зависимости от параметров. Это удобно, но иногда бывает нужно изменить сжатие и расширение по умолчанию. Тут тоже есть специальные свойства:

- flex-grow — управляет расширением отдельного элемента. Значение задается как целое положительное число, по умолчанию оно 1 для каждого элемента. Если увеличить это значение, элемент начнет «требовать» себе больше места на оси, чем другие;
- flex-shrink — управляет сжатием отдельного элемента. По умолчанию значение для всех элементов — 1. Если поставить его как 0, элемент вообще не будет сжиматься, а если увеличить до 2, 3 и так далее — будет сжиматься сильнее других;
- flex-basis — устанавливает «базовый» размер элемента, относительно которого он будет расширяться и сжиматься.

Эти свойства нужны, когда важно сделать какой-то элемент больше или меньше других.

## **Для каких элементов обычно используют Flexbox**

Чаще всего флексы применяют там, где нужна «одномерная» сетка, то есть надо управлять расположением элементов только в одном направлении. Обычно это микросетки, мелкие элементы внутри какого-то большого блока. Для разметки крупных блоков на странице целиком флекс используют редко — это зачастую не очень удобно.

Кроме флексов, есть еще два способа располагать элементы. Первый — обычный блочный метод, когда блоки просто следуют друг за другом по вертикали: отступы между ними прописываются вручную, ничего не сжимается и не расширяется. Второй способ — грид-контейнеры, еще одна возможность CSS 3.0. С их помощью можно создавать двумерные сетки и по-разному располагать элементы внутри: например, вверху один широкий блок, а внизу два или три маленьких.

==Обычно для каждого блока верстальщик сам определяет, флексом он будет описываться или гридом. Ни один из этих способов не лучше — они просто предназначены для разных случаев.==

## **Преимущества Flexbox**

**Гибкость.** Возможность гибко управлять положением элементов — одно из главных преимуществ флекса. Благодаря ей он намного удобнее предыдущих способов построения сеток и позволяет не задумываться о размерах элементов вплоть до пикселя. С помощью Flexbox можно настроить автоматическое выравнивание и распределение по оси, а не задавать все эти параметры вручную.

**Адаптивность.** Flex отлично подходит для создания «резиновой» верстки, когда размеры элементов меняются в зависимости от параметров экрана. Такая верстка — хорошая практика по сравнению со статической, потому что лучше выглядит на разных устройствах. У нее ниже риск «развалиться» и стать нефункциональной.

**Удобное выравнивание.** Выравнивание во флексбоксе — мощный и очень удобный инструмент. С его помощью можно в пару строк реализовать вещи, которые без флекса отняли бы куда больше усилий. Поэтому иногда флексбокс используют даже не по назначению — например, для точного центрирования контента внутри блока.

**Хорошая поддержка в браузерах.** Flexbox появился довольно давно, и сейчас его поддерживают все современные браузеры. Поддержка отсутствует только в очень старых версиях интернет-обозревателей, которыми сейчас мало кто пользуется. В большинстве компаний считается, что поддерживать настолько старые браузеры уже не стоит.

**Адаптация под разные языки.** Выше мы рассказывали про значения reverse-row, reverse-column и reverse-wrap и говорили, что они используются редко. Но это справедливо только для латиницы и кириллицы. Во многих языках практикуется чтение справа налево — эти свойства отлично подходят для адаптации веб-страниц под такие языки. Особенно удобно, если страница существует на разных языках с разными способами чтения: например, на английском и фарси.

## **Недостатки Flexbox**

**Риск «выпадания».** Флекс не универсален. Большинство его минусов связано с позиционированием элементов. Из-за того, что оно автоматическое, возможны ситуации, когда что-то ломается или выпадает. Если такое случится, верстка станет непригодной к использованию.

Пример — тот же nowrap. Если перенос отключен, элементы автоматически сжимаются, и до определенного предела это выглядит красиво и удобно. Но если они слишком маленькие, пользователю становится неудобно. А если их много, а сжать уже не получается, невлезшие элементы «вывалятся» за пределы контейнера. Они могут даже уйти за пределы экрана, и тогда ими нельзя будет пользоваться.

Таких ситуаций можно избегать — заранее предусматривать в верстке защиту от переполнения. Например, сразу прописывать возможность переноса и его параметры.

**Риск «расползания верстки».** Есть и еще одна ситуация — явление, когда при определенных условиях элементы начинают располагаться некрасиво. Выше мы рассказывали, что свойство space-between хорошо подходит для «растягивания» элементов в горизонтальном меню и что тут есть деталь. Это один из примеров такого поведения.

Горизонтальное меню со space-between смотрится отлично, пока его пунктов не становится много. Включается защита от переполнения, и лишние пункты переносятся на другую строку. И тут space-between играет плохую службу: если лишних пунктов два, они «приклеиваются» к краям контейнера, а по центру остается пустая область. А изменить расположение элементов только по одной строке нельзя.

Поэтому стоит заранее думать о возможных изменениях в верстке и предусматривать такие моменты, чтобы флекс-контейнеры нормально смотрелись при любых разрешениях и условиях.

**Сложности с двумерными сетками.** С помощью флексбокса сложно создать функциональную и красивую двумерную сетку — такую, в которой элементы расположены не вдоль линии, а как бы на плоскости. Но для этого есть грид, поэтому в таких ситуациях предпочтительнее использовать его.

## **Как начать пользоваться Flexbox**

Сетки и флексы — это та часть верстки, к которой стоит переходить уже после ознакомления с основами [HTML](https://blog.skillfactory.ru/glossary/html/) и CSS. Без знания базовых принципов разобраться в особенностях флексбокса может быть сложно. Но основ достаточно, чтобы начать тренироваться: есть иллюстрации, демотренажеры и даже [игра](https://flexboxfroggy.com/#ru) для лучшего понимания флекса.

Узнать больше про верстку и фронтенд вы можете на наших курсах. Получите новую интересную профессию — станьте востребованным специалистом!