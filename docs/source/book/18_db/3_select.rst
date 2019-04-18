SELECT
~~~~~~

Оператор select позволяет запрашивать информацию в таблице.

Например:

.. code:: sql

    sqlite> SELECT * from switch;
    0010.A1AA.C1CC|sw1|Cisco 3750|London, Green Str
    0020.A2AA.C2CC|sw2|Cisco 3850|London, Green Str

``select *`` означает, что нужно вывести все поля таблицы. Следом
указывается, из какой таблицы запрашиваются данные: ``from switch``.

В данном случае в отображении таблицы не хватает названия полей.
Включить это можно с помощью команды ``.headers ON``.

.. code:: sql

    sqlite> .headers ON
    sqlite> SELECT * from switch;
    mac|hostname|model|location
    0010.A1AA.C1CC|sw1|Cisco 3750|London, Green Str
    0020.A2AA.C2CC|sw2|Cisco 3850|London, Green Str

Теперь отобразились заголовки, но, в целом, отображение не очень
приятное. Хотелось бы, чтобы все выводилось в виде колонок. За
форматирование вывода отвечает команда ``.mode``.

Режим ``.mode column`` включает отображение в виде колонок:

.. code:: sql

    sqlite> .mode column
    sqlite> SELECT * from switch;
    mac             hostname    model       location
    --------------  ----------  ----------  -----------------
    0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
    0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str

При желании можно выставить и ширину колонок. Для этого используется
команда ``.width``. Например, попробуйте выставить ``.width 20``.

Если нужно сделать так, чтобы эти параметры использовались по умолчанию,
добавьте их в файл ``.sqliterc`` в домашнем каталоге пользователя, под
которым Вы работаете.

Например, чтобы вывод заголовков столбцов и вывод столбцами
использовались по умолчанию, файл .sqliterc должен выглядеть так:

::

    .headers on
    .mode column

    В следующих подразделах вывод команд показан с включенными .headers
    on и .mode column
