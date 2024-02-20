Содержание

1. 1. [Установка](https://blog.skillfactory.ru/kak-napisat-veb-prilozhenie-dlya-demonstratsii-data-science-proekta-na-python/#установка)
2. 2. [Виджеты Streamlit](https://blog.skillfactory.ru/kak-napisat-veb-prilozhenie-dlya-demonstratsii-data-science-proekta-na-python/#виджеты-streamlit)
3. 3. [Пошаговое создание простого приложения](https://blog.skillfactory.ru/kak-napisat-veb-prilozhenie-dlya-demonstratsii-data-science-proekta-na-python/#пошаговое-создание-простого-приложения)
4. 4. [Возможные улучшения](https://blog.skillfactory.ru/kak-napisat-veb-prilozhenie-dlya-demonstratsii-data-science-proekta-na-python/#возможные-улучшения)
5. 5. [Заключение](https://blog.skillfactory.ru/kak-napisat-veb-prilozhenie-dlya-demonstratsii-data-science-proekta-na-python/#заключение)

Проект по машинному обучению никогда не будет по-настоящему завершен, пока мы не сможем продемонстрировать его результаты. В прошлом хорошо сделанной визуализации или небольшой презентации в Power Point было достаточно для демонстрации проекта, но сейчас, с появлением инструментов для создания дашбордов вроде RShiny или Dash, хороший data science-специалист должен иметь некоторое представление о веб-фреймворках.

Но проблема в том, что веб-фреймворки достаточно сложны в изучении. Я до сих пор путаюсь по всех этих [HTML](https://blog.skillfactory.ru/glossary/html/), CSS и Javascript, и даже что-то, что выглядит простым в исполнении, начинает работать только после большого количества проб и ошибок. Не говоря уж о том, что существует огромное количество способов сделать одно и то же, и это сбивает с толку людей, для которых веб-разработка является второстепенным навыком.

Так что же, мы обречены на изучение веб-фреймворков? Или на звонки нашим друзьям-разработчикам посреди ночи с глупыми вопросами?

И тут нам на выручку спешит Streamlit с обещанием позволить нам создавать веб-приложения, используя только Python. ==Следуя философии Python – «просто – лучше, чем сложно», Streamlit предельно упрощает создание приложений.==

В этой статье я хочу показать как создавать приложения для демонстрации data science-проектов с помощью Streamlit. Чтобы лучше понять архитектуру Streamlit, а также идеи, которые привели к его созданию, прочитайте [отличную статью](https://towardsdatascience.com/coding-ml-tools-like-you-code-ml-models-ddba3357eace) одного из разработчиков и основателей [Адриана Трёля](https://medium.com/u/39dfc90d7a34).

## Установка

Установить Streamlit очень просто, делается это с помощью одной команды:

```
pip install streamlit
```

Чтобы проверить успешно ли прошла установка, можно использовать

```
streamlit hello
```

После этой команды на экране должен появиться текст:

![[a838f664bc8626902a7185214a4fd039_MD5.jpg]]

Теперь вы можете перейти по локальному URL localhost:8501 в вашем браузере, чтобы увидеть Streamlit в действии. Разработчики предоставили несколько демо, которые будет интересно изучить. Не торопитесь и постарайтесь понять широкие возможности приложения, прежде чем вернуться к этой статье.

### Streamlit Hello World

Streamlit стремится сделать разработку приложений проще с использованием исключительно Python.

Давайте попробуем написать простое приложение, чтобы увидеть получилось ли это у них.

Я начну с простого приложения, которое мы назовем Streamlit Hello World. Просто скопируйте текст ниже в файл с названием helloworld.py:

```
import streamlit as st
x = st.slider('x')
st.write(x, 'squared is', x * x)
```

И используйте в терминале команду:

```
streamlit run helloworld.py
```

![[c5d2d1cbd4357a57346f99015fb061c1_MD5.jpg]]

Вуаля, вы должны увидеть простое приложение в своем браузере по адресу localhost:8501, которое позволит вам двигать слайдер для выбора числа и видеть результат возведения этого числа в квадрат.

Это было достаточно просто. В приложении выше мы использовали две функции из Streamlit:

- Виджет st.slider, который мы можем сдвигать, чтобы изменять вывод веб-приложения
- И универсальную команду st.write. Я в восторге от того, как она может писать все что угодно, включая графики, датафреймы и обыкновенный текст. Но мы еще поговорим об этом позже.

**Важно:** Помните, что каждый раз, когда мы меняем значение в виджете, все приложение перезапускается целиком.

## Виджеты Streamlit

Виджеты дают нам способ контролировать наше приложение. Лучше всего почитать о них в [документации](https://streamlit.io/docs/api.html) Streamlit, но я опишу несколько основных, которые вам возможно пригодятся.

### 1. Слайдер

```
streamlit.slider(label, min_value=None, max_value=None, value=None, step=None, format=None)
```

Мы уже видели st.slider в действии. Он может быть использован с указанием минимального и максимального значения, а также шага для получения входящих значений в заданном диапазоне.

### 2. Ввод текста

st.text_input — это самый простой способ получить на вход текст от пользователя — будь это какой-то URL или текст для анализа его тональности. Нужно только передать функции название текстового окна.

![[3e411010684ceb580b533a9909bf2460_MD5.png]]

Так будет выглядеть готовое приложение:

![[c2b40242c065a0b3b0b42746aaa9a0c8_MD5.jpg]]

**Совет:** Вы можете просто изменить файл helloworld.py и обновить браузер. Я обычно работаю таким образом: открываю и изменяю файл в Sublime Text и наблюдаю за изменениями в браузере расположенном в рядом с окном редактора.

### 3. Чекбокс

Один из вариантов использования чекбоксов — это показывать или прятать определенную часть приложения. Или, например, чекбокс может использоваться для выбора булевозначного параметра для функции. [st.checkbox()](https://streamlit.io/docs/api.html#streamlit.checkbox)использует только один аргумент — метку виджета. 

В этом приложении чекбокс используется для переключения условного оператора:

```
import streamlit as st
import pandas as pd
import numpy as np
df = pd.read_csv("football_data.csv")
if st.checkbox('Show dataframe'):
   st.write(df)
```

![[e904ff37909119a51421760174f7a4d5_MD5.jpg]]

Готовый чекбокс

### 4. Выпадающий список

Мы можем использовать [st.selectbox](https://streamlit.io/docs/api.html#streamlit.selectbox) для выбора из списка. Обычно эта функция используется для создания выпадающего списка, который позволяет выбрать значение.

```
import streamlit as st
import pandas as pd
import numpy as np
df = pd.read_csv("football_data.csv")
option = st.selectbox(
   'Which Club do you like best?',
    df['Club'].unique())
'You selected: ', option
```

![[500c5a0272b4211646b545369bbaac11_MD5.jpg]]

Результат

### 5. Выбор нескольких значений

Мы также можем выбрать несколько значений из выпадающего списка. Для этого можно использовать st.multiselect, которая сохранит несколько выбранных значений в виде списка в переменную options:

```
import streamlit as st
import pandas as pd
import numpy as np
df = pd.read_csv("football_data.csv")
options = st.multiselect(
'What are your favorite clubs?', df['Club'].unique())
st.write('You selected:', options)
```

![[5daaaca78224f77023a158c82c657bfa_MD5.jpg]]

Результат

## Пошаговое создание простого приложения

Вот мы и разобрали самые важные виджеты. Теперь давайте создадим простое приложение с использованием нескольких из них.

Начнем с простого: попробуем визуализировать данные футбольных матчей с использованием Streamlit. Это достаточно легко сделать с помощью вышеописанных виджетов:

![[3385187386e05bf33e84a27e0332b4e2_MD5.png]]

![[ef489901582b2c0f7c8a22b353308e5a_MD5.jpg]]

Это было легко. Но результат выглядит достаточно просто, может стоит добавить графиков?

Streamlit на данный момент поддерживает многие библиотеки для построения графиков. Среди них **_Plotly, Bokeh, [Matplotlib](https://blog.skillfactory.ru/glossary/matplotlib/), Altair_** и **_Vega charts._** Кроме того, работает и **_Plotly Express_**, хотя это и не указано в документации. Помимо внешних библиотек, в Streamlit встроены и свои нативные типы графиков, например st.line_chart и st.area_chart.

Сейчас мы будем работать с plotly_express. Вот код для нашего простого приложения. Мы используем всего 4 вызова Streamlit, все остальное — это простой Python:

```
import streamlit as st
import pandas as pd
import numpy as np
import plotly_express as px
df = pd.read_csv("football_data.csv")
clubs = st.multiselect('Show Player for clubs?', df['Club'].unique())
nationalities = st.multiselect('Show Player from Nationalities?', df['Nationality'].unique())
new_df = df[(df['Club'].isin(clubs)) & (df['Nationality'].isin(nationalities))]
st.write(new_df)
# create figure using plotly express
fig = px.scatter(new_df, x ='Overall',y='Age',color='Name')
# Plot!
st.plotly_chart(fig)
```

![[b501b13f61b856119c8d992eba4ac0fc_MD5.jpg]]

Результат

## Возможные улучшения

В начале статьи я говорил, что каждый раз, когда мы меняем какой-либо виджет, все приложение полностью запускается заново. Это нецелесообразно, если мы создаем приложения для моделей глубокого обучения или сложных моделей машинного обучения. Streamlit решает эту проблему через кэширование.

### 1. Кэширование

В нашем базовом приложении мы читаем [датафрейм pandas](https://blog.skillfactory.ru/glossary/pandas/) снова всякий раз, когда значение изменяется. И хотя это работает с маленькими базами данных как та, которую мы используем сейчас, но это не сработает с big data или если нам понадобится проводить большое количество манипуляций с данными. Использовать кэширование можно через функцию st.cache:

```
import streamlit as st
import pandas as pd
import numpy as np
import plotly_express as px
df = st.cache(pd.read_csv)("football_data.csv")
```

Для более сложных и требующих много времени для вычисления функций, которые должны запускаться только один раз (например, загрузка больших моделей глубокого обучения), можно использовать такой вариант:

```
@st.cache
def complex_func(a,b):
   DO SOMETHING COMPLEX
# Won't run again and again.
complex_func(a,b)
```

Когда мы помечаем функцию кэширующим декоратором, всякий раз, когда эта функция вызывается, Streamlit будет проверять входные параметры функции.

Если Streamlit видит эти параметры впервые, он запустит функцию и сохранит результат в локальном кэше.

Когда функция будет вызывана в следующий раз, если эти параметры не изменились, Streamlit будет знать, что выполнение функции можно пропустить — он просто использует результаты из кэша.

### 2. Сайдбар

В зависимости от ваших предпочтений, вы можете сделать что-то похожее на Rshiny dashboards и переместить свои виджеты в боковую панель, чтобы придать приложению более аккуратный вид. Для этого просто добавьте st.sidebar в код вашего виджета:

```
import streamlit as st
import pandas as pd
import numpy as np
import plotly_express as px
df = st.cache(pd.read_csv)("football_data.csv")
clubs = st.sidebar.multiselect('Show Player for clubs?', df['Club'].unique())
nationalities = st.sidebar.multiselect('Show Player from Nationalities?', df['Nationality'].unique())
new_df = df[(df['Club'].isin(clubs)) & (df['Nationality'].isin(nationalities))]
st.write(new_df)
# Create distplot with custom bin_size
fig = px.scatter(new_df, x ='Overall',y='Age',color='Name')
# Plot!
st.plotly_chart(fig)
```

![[c300ae36d274dbdea9b817b98ab5bb6c_MD5.jpg]]

Так выглядят готовые боковые виджеты.

### 3. Markdown

Мне нравится использовать Markdown. Мне он кажется менее громоздким, чем HTML, и более подходящим для работы в data science. Можем ли мы использовать Markdown в Streamlit-приложении?

И ответ: да, можем. Есть несколько способов это осуществить. На мой взгляд, лучший вариант — это использовать [Magic commands](https://streamlit.io/docs/api.html#id1). Эти команды позволят вам использовать Markdown точно так же как и простое комментирование. Но вы также можете воспользоваться командой _st.markdown._

```
import streamlit as st
import pandas as pd
import numpy as np
import plotly_express as px
'''
# Club and Nationality App
This very simple webapp allows you to select and visualize players from certain clubs and certain nationalities.
'''
df = st.cache(pd.read_csv)("football_data.csv")
clubs = st.sidebar.multiselect('Show Player for clubs?', df['Club'].unique())
nationalities = st.sidebar.multiselect('Show Player from Nationalities?', df['Nationality'].unique())
new_df = df[(df['Club'].isin(clubs)) & (df['Nationality'].isin(nationalities))]
st.write(new_df)
# Create distplot with custom bin_size
fig = px.scatter(new_df, x ='Overall',y='Age',color='Name')
'''
### Here is a simple chart between player age and overall
'''
st.plotly_chart(fig)
```

## Заключение

Streamlit сделал процесс создания приложений значительно проще, и я однозначно рекомендую его к использования.

В этой статье я показал очень простое веб-приложение, но на деле возможности использования безграничны. В качестве примера хочу привести GAN-генератор лиц с сайта Streamlit. Это приложение работает на основе тех же идей виджетов и кэширования.

Мне нравятся цветовая гамма и стили, которые разработчики Streamlit используют по умолчанию, плюс мне кажется, Streamlit удобнее в использовании, чем Dash, который я использовал до этого для создания демо. К тому же Streamlit позволяет добавлять [аудио](https://streamlit.io/docs/api.html#display-interactive-widgets) и видео в свои приложения.

Отдельный бонус — это то, что Streamlit является бесплатным open source решением, которое работает прямо из коробки. В прошлом мне приходилось обращаться к друзьям-разработчикам для любого изменения в демо или презентации; теперь все это я могу относительно просто проделать сам.

Я не знаю, будут ли Streamlit-приложения хорошо работать в производственной среде, но они отлично подходят для proof of concept и демо. Я планирую продолжить работать со Streamlit, и, учитывая простоту использования и возможности, которые он предоставляет, я думаю, вам тоже стоит об этом задуматься.Вы можете найти код финальной версии приложения [здесь](https://github.com/MLWhiz/streamlit_football_demo).