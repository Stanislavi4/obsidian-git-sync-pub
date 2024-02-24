---
created: 2024-02-24T07:23:29 (UTC +06:00)
tags: []
source: https://blog.skillfactory.ru/5-prostyh-sposobov-vizualizacii-dannyh-na-python-pishem-kod/#%D0%BA%D0%B0%D0%BA-%D1%8D%D1%82%D0%BE-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%D0%B5%D1%82
author: 
---

# 5 простых способов визуализации данных на Python. Пишем код - Статьи и полезные материалы на тему «Uncategorized» - SkillFactory

> ## Excerpt
> Способоы визуализации данных для Data Science с помощью Python – Блог SkillFactory

---
При анализе данных очень важна наглядность. Чаще всего недостаточно просто собрать что-то и сгруппировать — данные еще нужно показать. А ещё лучше отобразить данные так, чтобы одного взгляда было достаточно для принятия решений.

## Как это работает?

Для визуализации данных в Python чаще всего используют библиотеки [Matplotlib](https://blog.skillfactory.ru/glossary/matplotlib/), [Bokeh](https://bokeh.pydata.org/en/latest/), [pygal](http://www.pygal.org/en/latest/index.html), [seaborn](https://seaborn.pydata.org/). А как их использовать, в каком случае и использовать ли вообще, сейчас разберемся.

Подключим библиотеки [Matplotlib](https://mode.com/python-tutorial/libraries/matplotlib/) и [seaborn](https://seaborn.pydata.org/) к проекту:

![](5%20%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D1%85%20%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BE%D0%B2%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20Python.%20%D0%9F%D0%B8%D1%88%D0%B5%D0%BC%20%D0%BA%D0%BE%D0%B4%20-%20%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D0%B8%20%D0%B8%20%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5%20%D0%BC%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D1%8B%20%D0%BD%D0%B0%20%D1%82%D0%B5%D0%BC%D1%83%20%C2%ABUncategorized%C2%BB%20-%20SkillFactory/carbon-10.png)

Сначала нам нужны данные для экспериментов: я нашла несколько вакансий на должность веб-разработчика в разных странах и взяла предлагаемую зарплату. Получились такие словари:

![](5%20%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D1%85%20%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BE%D0%B2%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20Python.%20%D0%9F%D0%B8%D1%88%D0%B5%D0%BC%20%D0%BA%D0%BE%D0%B4%20-%20%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D0%B8%20%D0%B8%20%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5%20%D0%BC%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D1%8B%20%D0%BD%D0%B0%20%D1%82%D0%B5%D0%BC%D1%83%20%C2%ABUncategorized%C2%BB%20-%20SkillFactory/carbon-11-486x1024.png)

Теперь нужно решить что именно мы хотим узнать:

-   Какие у нас есть страны и заплаты? (столбчатая диаграмма)
-   Какая самая распространенная зарплата для веб-разработчика? (гистограмма или тепловая карта)
-   Насколько разные зарплаты предлагают? (диаграмма размаха)
-   Как стать веб-разработчиком? (радиальная диаграмма)

### 1\. Столбчатая диаграмма

_Столбчатая диаграмма_ — это как таблица, но для презентаций. У нас есть данные по странам за разные годы, выберем нужный словарь и выведем:

![](5%20%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D1%85%20%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BE%D0%B2%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20Python.%20%D0%9F%D0%B8%D1%88%D0%B5%D0%BC%20%D0%BA%D0%BE%D0%B4%20-%20%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D0%B8%20%D0%B8%20%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5%20%D0%BC%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D1%8B%20%D0%BD%D0%B0%20%D1%82%D0%B5%D0%BC%D1%83%20%C2%ABUncategorized%C2%BB%20-%20SkillFactory/carbon-12-1024x443.png)

### **2\. Гистограмма**

_Гистограмма_ — один из вариантов столбчатой диаграммы для оценки распределения значений. Можно оценить частоту появления одного значения или наложить несколько для сравнения. Посмотрим распределение зарплат:

![](5%20%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D1%85%20%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BE%D0%B2%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20Python.%20%D0%9F%D0%B8%D1%88%D0%B5%D0%BC%20%D0%BA%D0%BE%D0%B4%20-%20%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D0%B8%20%D0%B8%20%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5%20%D0%BC%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D1%8B%20%D0%BD%D0%B0%20%D1%82%D0%B5%D0%BC%D1%83%20%C2%ABUncategorized%C2%BB%20-%20SkillFactory/carbon-13.png)

### 3\. Тепловая карта

_Тепловая карта_ разворачивает гистограмму в другую плоскость. Здесь мы цветом показываем частоту разных значений. Посмотрим количество разработчиков по странам. Точного значения мы не знаем, набросаем случайные числа в csv-файл:

![](5%20%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D1%85%20%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BE%D0%B2%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20Python.%20%D0%9F%D0%B8%D1%88%D0%B5%D0%BC%20%D0%BA%D0%BE%D0%B4%20-%20%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D0%B8%20%D0%B8%20%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5%20%D0%BC%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D1%8B%20%D0%BD%D0%B0%20%D1%82%D0%B5%D0%BC%D1%83%20%C2%ABUncategorized%C2%BB%20-%20SkillFactory/carbon-14.png)

Тогда код будет выглядеть так:

![](5%20%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D1%85%20%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BE%D0%B2%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20Python.%20%D0%9F%D0%B8%D1%88%D0%B5%D0%BC%20%D0%BA%D0%BE%D0%B4%20-%20%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D0%B8%20%D0%B8%20%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5%20%D0%BC%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D1%8B%20%D0%BD%D0%B0%20%D1%82%D0%B5%D0%BC%D1%83%20%C2%ABUncategorized%C2%BB%20-%20SkillFactory/carbon-15-1024x422.png)

![](5%20%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D1%85%20%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BE%D0%B2%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20Python.%20%D0%9F%D0%B8%D1%88%D0%B5%D0%BC%20%D0%BA%D0%BE%D0%B4%20-%20%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D0%B8%20%D0%B8%20%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5%20%D0%BC%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D1%8B%20%D0%BD%D0%B0%20%D1%82%D0%B5%D0%BC%D1%83%20%C2%ABUncategorized%C2%BB%20-%20SkillFactory/image-5.png)

### 4\. Диаграмма размаха

_Диаграмма размаха_ ещё называется «ящик с усами» — смотрим среднее значение и «усами» показываем максимальные отклонения от него. Построим так:

![](5%20%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D1%85%20%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BE%D0%B2%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20Python.%20%D0%9F%D0%B8%D1%88%D0%B5%D0%BC%20%D0%BA%D0%BE%D0%B4%20-%20%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D0%B8%20%D0%B8%20%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5%20%D0%BC%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D1%8B%20%D0%BD%D0%B0%20%D1%82%D0%B5%D0%BC%D1%83%20%C2%ABUncategorized%C2%BB%20-%20SkillFactory/carbon-16-1024x423.png)

![](5%20%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D1%85%20%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BE%D0%B2%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20Python.%20%D0%9F%D0%B8%D1%88%D0%B5%D0%BC%20%D0%BA%D0%BE%D0%B4%20-%20%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D0%B8%20%D0%B8%20%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5%20%D0%BC%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D1%8B%20%D0%BD%D0%B0%20%D1%82%D0%B5%D0%BC%D1%83%20%C2%ABUncategorized%C2%BB%20-%20SkillFactory/image-6.png)

### 5\. Радиальная диаграмма

_Радиальная диаграмма_ удобна, когда мы хотим сравнить параметры у одной сущности. Например, какие скиллы нужны веб-разработчику:

![](5%20%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D1%85%20%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BE%D0%B2%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20Python.%20%D0%9F%D0%B8%D1%88%D0%B5%D0%BC%20%D0%BA%D0%BE%D0%B4%20-%20%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D0%B8%20%D0%B8%20%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5%20%D0%BC%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D1%8B%20%D0%BD%D0%B0%20%D1%82%D0%B5%D0%BC%D1%83%20%C2%ABUncategorized%C2%BB%20-%20SkillFactory/carbon-17-783x1024.png)

![](5%20%D0%BF%D1%80%D0%BE%D1%81%D1%82%D1%8B%D1%85%20%D1%81%D0%BF%D0%BE%D1%81%D0%BE%D0%B1%D0%BE%D0%B2%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85%20%D0%BD%D0%B0%20Python.%20%D0%9F%D0%B8%D1%88%D0%B5%D0%BC%20%D0%BA%D0%BE%D0%B4%20-%20%D0%A1%D1%82%D0%B0%D1%82%D1%8C%D0%B8%20%D0%B8%20%D0%BF%D0%BE%D0%BB%D0%B5%D0%B7%D0%BD%D1%8B%D0%B5%20%D0%BC%D0%B0%D1%82%D0%B5%D1%80%D0%B8%D0%B0%D0%BB%D1%8B%20%D0%BD%D0%B0%20%D1%82%D0%B5%D0%BC%D1%83%20%C2%ABUncategorized%C2%BB%20-%20SkillFactory/image-7.png)

## Что делать дальше?

Мы рассмотрели самые простые варианты визуализирования данных, но для некоторых задач этого может быть недостаточно, поэтому:

1.  Читайте документацию библиотек [Maptolib](https://mode.com/python-tutorial/libraries/matplotlib/), [Bokeh](https://bokeh.pydata.org/en/latest/), [pygal](http://www.pygal.org/en/latest/index.html), [seaborn](https://seaborn.pydata.org/).
2.  Следите за новыми разработками для визуализации.
3.  Записывайтесь на [курс по Python для анализа данных](https://skillfactory.ru/python-analytics?utm_source=blog&utm_medium=referral&utm_campaign=article_py_viz&utm_content=5-prostyh-sposobov-vizualizacii-dannyh-na-python-pishem-kod&utm_term=23-04-2019) от SkillFactory.
