Получение результатов запроса
-----------------------------

Для получения результатов запроса в sqlite3 есть несколько способов: 

* использование методов ``fetch`` - в зависимости от метода 
  возвращаются одна, несколько или все строки 
* использование курсора как итератора - возвращается итератор

Метод fetchone
^^^^^^^^^^^^^^

Метод fetchone возвращает одну строку данных.

Пример получения информации из базы данных sw_inventory.db:

.. code:: python

    In [16]: import sqlite3

    In [17]: connection = sqlite3.connect('sw_inventory.db')

    In [18]: cursor = connection.cursor()

    In [19]: cursor.execute('select * from switch')
    Out[19]: <sqlite3.Cursor at 0x104eda810>

    In [20]: cursor.fetchone()
    Out[20]: ('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str')

Обратите внимание, что хотя запрос SQL подразумевает, что запрашивалось
всё содержимое таблицы, метод fetchone вернул только одну строку.

Если повторно вызвать метод, он вернет следующую строку:

.. code:: python

    In [21]: print(cursor.fetchone())
    ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str')

Аналогичным образом метод будет возвращать следующие строки. После
обработки всех строк метод начинает возвращать None.

За счет этого метод можно использовать в цикле, например, так:

.. code:: python

    In [22]: cursor.execute('select * from switch')
    Out[22]: <sqlite3.Cursor at 0x104eda810>

    In [23]: while True:
       ...:     next_row = cursor.fetchone()
       ...:     if next_row:
       ...:         print(next_row)
       ...:     else:
       ...:         break
       ...:
    ('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str')
    ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str')
    ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str')
    ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0001', 'sw5', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0002', 'sw6', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0003', 'sw7', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0004', 'sw8', 'Cisco 3750', 'London, Green Str')

Метод fetchmany
^^^^^^^^^^^^^^^

Метод fetchmany возвращает список строк данных.

Синтаксис метода:

.. code:: python

    cursor.fetchmany([size=cursor.arraysize])

С помощью параметра size можно указывать, какое количество строк
возвращается. По умолчанию параметр size равен значению
cursor.arraysize:

.. code:: python

    In [24]: print(cursor.arraysize)
    1

Например, таким образом можно возвращать по три строки из запроса:

.. code:: python


    In [25]: cursor.execute('select * from switch')
    Out[25]: <sqlite3.Cursor at 0x104eda810>

    In [26]: from pprint import pprint

    In [27]: while True:
        ...:     three_rows = cursor.fetchmany(3)
        ...:     if three_rows:
        ...:         pprint(three_rows)
        ...:     else:
        ...:         break
        ...:
    [('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str'),
     ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str'),
     ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str')]
    [('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str'),
     ('0000.1111.0001', 'sw5', 'Cisco 3750', 'London, Green Str'),
     ('0000.1111.0002', 'sw6', 'Cisco 3750', 'London, Green Str')]
    [('0000.1111.0003', 'sw7', 'Cisco 3750', 'London, Green Str'),
     ('0000.1111.0004', 'sw8', 'Cisco 3750', 'London, Green Str')]

Метод выдает нужное количество строк, а если строк осталось меньше, чем
параметр size, то оставшиеся строки.

Метод fetchall
^^^^^^^^^^^^^^

Метод fetchall возвращает все строки в виде списка:

.. code:: python

    In [28]: cursor.execute('select * from switch')
    Out[28]: <sqlite3.Cursor at 0x104eda810>

    In [29]: cursor.fetchall()
    Out[29]:
    [('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str'),
     ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str'),
     ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str'),
     ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str'),
     ('0000.1111.0001', 'sw5', 'Cisco 3750', 'London, Green Str'),
     ('0000.1111.0002', 'sw6', 'Cisco 3750', 'London, Green Str'),
     ('0000.1111.0003', 'sw7', 'Cisco 3750', 'London, Green Str'),
     ('0000.1111.0004', 'sw8', 'Cisco 3750', 'London, Green Str')]

Важный аспект работы метода - он возвращает все оставшиеся строки.

То есть, если до метода fetchall использовался, например, метод
fetchone, то метод fetchall вернет оставшиеся строки запроса:

.. code:: python

    In [30]: cursor.execute('select * from switch')
    Out[30]: <sqlite3.Cursor at 0x104eda810>

    In [31]: cursor.fetchone()
    Out[31]: ('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str')

    In [32]: cursor.fetchone()
    Out[32]: ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str')

    In [33]: cursor.fetchall()
    Out[33]:
    [('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str'),
     ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str'),
     ('0000.1111.0001', 'sw5', 'Cisco 3750', 'London, Green Str'),
     ('0000.1111.0002', 'sw6', 'Cisco 3750', 'London, Green Str'),
     ('0000.1111.0003', 'sw7', 'Cisco 3750', 'London, Green Str'),
     ('0000.1111.0004', 'sw8', 'Cisco 3750', 'London, Green Str')]

Метод fetchmany в этом аспекте работает аналогично.

