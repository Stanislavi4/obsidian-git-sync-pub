Содержание

1. 1. [Кто работает с LDAP](https://blog.skillfactory.ru/glossary/ldap/#кто-работает-с-ldap)
2. 2. [Для чего нужен протокол LDAP](https://blog.skillfactory.ru/glossary/ldap/#для-чего-нужен-протокол-ldap)
3. 3. [Хранение данных в каталоге](https://blog.skillfactory.ru/glossary/ldap/#хранение-данных-в-каталоге)
4. 4. [Что входит в LDAP](https://blog.skillfactory.ru/glossary/ldap/#что-входит-в-ldap)
5. 5. [Протокол LDAP и устройство DIT](https://blog.skillfactory.ru/glossary/ldap/#протокол-ldap-и-устройство-dit)

LDAP, или Lightweight Directory Access Protocol, — это протокол быстрого доступа к каталогам. Протоколом называют набор правил и соглашений, которые определяют, как будут передаваться и приниматься данные. LDAP позволяет получать и принимать через сеть данные из каталогов с иерархической структурой.

Каталог — это особый вид базы данных, подобие адресной книги, где содержатся краткие описательные сведения о каких-либо сущностях. Служба каталогов, соответственно, — это ПО, которое обслуживает каталоги. То, по какому принципу будет структурировано содержимое, определяют стандарты.

LDAP — изначально набор правил для обращений к службе каталогов по сети. Он был рассчитан на старый почтовый стандарт X.500, но со временем применение расширилось. С помощью LDAP можно искать записи и сравнивать их между собой, добавлять, редактировать или удалять, проводить аутентификацию. Протокол использует в работе TCP/IP.

Существует понятие LDAP-сервера — это сервер, где хранится база каталогов LDAP.

## **Кто работает с LDAP**

- Сетевые и системные администраторы, которые настраивают сети и связи между разными системами.
- Системные архитекторы, инженеры [ЦОД](https://blog.skillfactory.ru/glossary/czod/).
- DevOps-инженеры, которые занимаются построением рабочей сетевой инфраструктуры — окружения для разработчиков.
- Специалисты по системной безопасности, которые также часто имеют дело с передачей данных через сеть.
## **Для чего нужен протокол LDAP**

- Определение, каким образом данные будут представлены в системе LDAP, например на сервере.
- Доступ к этим данным по определенным правилам. LDAP сам по себе не описывает архитектуру сервера, он только показывает, по какому принципу тот передает информацию.
- Чтение, запись, добавление новых данных или модификация существующих.
- Аутентификация — выдача определенных прав доступа только после авторизации в системе.
- Обеспечение безопасности данных — LDAP позволяет гибко настраивать права доступа и защищать информацию при передаче по сети.
- Хранение баз данных, которые не слишком часто обновляются. Обычно это списки контактов, перечень страниц клиентов и прочие подобные базы.
- Создание «обертки» вокруг стандартной реляционной базы данных — иногда так оптимизируют доступ к ней.

## **Хранение данных в каталоге**

Каталог представляет информацию как пары «ключ—значение». Например, ключ — имя, а значение — Иван. Это обычно описательные сведения: например, таким образом можно хранить досье или каталоги товаров. Служба каталогов оптимизирует операции чтения и поиска по записям. Поэтому представление данных хорошо подходит для информации, которую часто читают и к которой часто обращается большое количество пользователей. Служба каталогов позволяет в таких случаях быстро получить сведения. Для записей, которые часто изменяются, каталоги не подойдут.

В реальных проектах службы каталогов используют для хранения данных о пользователях, информации об организации, ее сотрудниках или имуществе. Информация должна быть легкодоступной, описательной, а уже существующие данные редко изменяются.

Пример реальной службы каталогов — Active Directory, решение от Microsoft, которое реализует подход. Об Active Directory часто можно услышать в связи с LDAP, так как изначально она разрабатывалась под этот протокол, но позже стала поддерживать и другие способы доступа. Служба используется в связке с Windows Server.

## **Что входит в LDAP**

Системы, основанные на использовании LDAP-протокола, обычно сложные. Данные сформированы в виде иерархической древовидной структуры, в отличие от более привычных реляционных баз данных, в которых информация связана друг с другом отношениями. Подробнее про это можно прочитать в [статье про БД](https://blog.skillfactory.ru/glossary/baza-dannyh/).

**Атрибуты.** Это своеобразные единицы, в которых хранится информация. Выше мы писали, что сведения в каталоге представлены в виде пар из ключей и значений. Одна такая пара и есть атрибут. Например, запись вида «имя: Василий» — это атрибут. В таком формате хранится большая часть фактической, значимой информации в каталоге. Атрибут — это информационная единица. Остальные составные компоненты — в основном структурные и иерархические, они отвечают не за саму информацию, а за структуру ее хранения.

У атрибутов в каталогах LDAP есть особенность: имена ключей предопределены заранее в зависимости от того, как даны классы в записи.

**Записи.** Атрибуты не находятся в каталоге просто так — они собираются в кластеры, которые называются записями. Если атрибут — аналог поля и значения в реляционных базах данных, то запись — это аналог строки. В ней хранятся атрибуты какой-либо конкретной сущности.

Например, атрибуты «имя: Василий», «фамилия: Иванов», «должность: менеджер» могут быть составными частями записи об отдельном сотруднике. Для каждого сотрудника в каталоге можно иметь отдельную запись.

Записи могут принадлежать к каким-либо классам. В зависимости от этого у них могут быть те или иные атрибуты.

**DIT.** Это аббревиатура, которая расшифровывается как Data (Directory) Information Trees — информационные деревья каталогов. Это древовидные структуры, которые определяют иерархию между записями. Можно провести аналогию с файловой системой — назначение у DIT такое же. Деревья нужны, чтобы структурировать информацию. Каждая запись в LDAP-каталоге будет иметь отдельное место в DIT.

Насчет расположения записей есть определенные правила. У каждой из них может быть «предок», родительский элемент, но только один, зато неограниченное количество «потомков». Исключение — корневая запись, у которой нет родительского элемента. Хорошей аналогией опять же может служить файловая система любой ОС, где в «родительских» папках находятся дочерние.

Записи могут хранить практически любую информацию, поэтому иногда DIT-деревья используются как аналог файловой системы: некоторые «группирующие» родительские записи используются только для организации дочерних, то есть ведут себя как папки в операционной системе.

**Классы объектов.** Выше мы упомянули, что записи могут относиться к каким-либо классам. Класс в программировании — это шаблон для объекта, код, описывающий «базовую» сущность. Это понятие пришло из [объектно-ориентированного программирования](https://blog.skillfactory.ru/glossary/oop-obektno-orientirovannoe-programmirovanie/).

Классы объектов в LDAP, соответственно, определяют «тип» записи и по сути представляют собой группы связанных атрибутов. Например, можно создать класс «сотрудник» и указать, что у объектов этого класса будут атрибуты «имя», «фамилия», «должность», «срок службы» и так далее. После этого можно будет создать запись, определить для нее класс «сотрудник» и потом использовать атрибуты «сотрудника» внутри этой записи.

Классы нужны для организации и для большей четкости структуры. У них может быть своя иерархия: от базового класса можно унаследовать более специфические. Например, можно создать классы «сотрудник отдела продаж» или «сотрудник отдела развития» как дочерние от основного класса «сотрудник». Им можно добавлять дополнительные атрибуты и свойства.

**Схемы.** Схема, или schema, — это конструкция, в которой определены классы и атрибуты. Чтобы создать класс, его нужно описать внутри схемы — написать, как он будет называться, какие у него будут атрибуты, добавить дополнительные свойства. Там же описываются различные атрибуты: можно указать, к каким классам они могут относиться и какие значения принимать.

В одном DIT может быть множество схем, при этом они имеют возможность ссылаться друг на друга и требовать, например, атрибуты и свойства из другой схемы. Это не запрещено.

Благодаря такому построению данных в DIT можно гибко создавать записи с различными атрибутами, использовать шаблоны и «наследовать» от них дополнительные классы. Это лишь малая часть того, как устроено хранение данных в LDAP-системах, — но основная.

## **Протокол LDAP и устройство DIT**

Когда в DIT создается новая запись, она сразу же «прикрепляется» к уже существующей — другая запись становится ее «родителем». Исключение составляет, как уже говорилось, самая верхняя запись. Она обычно не информационная, это просто метка, которая обозначает корневую точку — например, название компании.

Записей без родителей, кроме корневой, быть не может. Так создается древовидная структура. Именно с ней работает протокол LDAP, который получает, ищет и модифицирует данные.

LDAP проходит по «дереву» и находит в нем нужные данные, причем позволяет сделать это относительно быстро и безопасно. Протокол использует стандартные методы доступа — это значит, что он относительно независим и разные реализации LDAP можно применять с одним и тем же деревом с одинаковым результатом.

Протокол оптимизирован на чтение, причем на чтение слева направо, а не сверху вниз. Это значит, что слишком глубокая вложенность в DIT может навредить скорости поиска.