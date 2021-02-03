Выполнение команд SQL
----------------------

Для выполнения команд SQL в модуле есть несколько методов: 

* ``execute`` - метод для выполнения одного выражения SQL 
* ``executemany`` - метод позволяет выполнить одно выражение SQL для 
  последовательности параметров (или для итератора) 
* ``executescript`` - метод позволяет выполнить несколько выражений SQL за один раз

Метод execute
^^^^^^^^^^^^^

Метод execute позволяет выполнить одну команду SQL.

Сначала надо создать соединение и курсор:

.. code:: python

    In [1]: import sqlite3

    In [2]: connection = sqlite3.connect('sw_inventory.db')

    In [3]: cursor = connection.cursor()

Создание таблицы switch с помощью метода execute:

.. code:: python

    In [4]: cursor.execute("create table switch (mac text not NULL primary key, hostname text, model text, location text)")
    Out[4]: <sqlite3.Cursor at 0x1085be880>

Выражения SQL могут быть параметризированы - вместо данных можно
подставлять специальные значения. За счет этого можно использовать одну
и ту же команду SQL для передачи разных данных.

Например, таблицу switch нужно заполнить данными из списка data:

.. code:: python

    In [5]: data = [
       ...: ('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str'),
       ...: ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str'),
       ...: ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str'),
       ...: ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')]

Для этого можно использовать запрос вида:

.. code:: python

    In [6]: query = "INSERT into switch values (?, ?, ?, ?)"

Знаки вопроса в команде используются для подстановки данных, которые
будут передаваться методу execute.

Теперь можно передать данные таким образом:

.. code:: python

    In [7]: for row in data:
       ...:     cursor.execute(query, row)
       ...:

Второй аргумент, который передается методу execute, должен быть
кортежем. Если нужно передать кортеж с одним элементом, используется
запись ``(value, )``.

Чтобы изменения были применены, нужно выполнить commit (обратите
внимание, что метод commit вызывается у соединения):

.. code:: python

    In [8]: connection.commit()

Теперь при запросе из командной строки sqlite3, можно увидеть эти
строки в таблице switch:

::

    $ litecli sw_inventory.db
    Version: 1.0.0
    Mail: https://groups.google.com/forum/#!forum/litecli-users
    Github: https://github.com/dbcli/litecli
    sw_inventory.db> SELECT * from switch;
    +----------------+----------+------------+-------------------+
    | mac            | hostname | model      | location          |
    +----------------+----------+------------+-------------------+
    | 0000.AAAA.CCCC | sw1      | Cisco 3750 | London, Green Str |
    | 0000.BBBB.CCCC | sw2      | Cisco 3780 | London, Green Str |
    | 0000.AAAA.DDDD | sw3      | Cisco 2960 | London, Green Str |
    | 0011.AAAA.CCCC | sw4      | Cisco 3750 | London, Green Str |
    +----------------+----------+------------+-------------------+
    4 rows in set
    Time: 0.039s
    sw_inventory.db>


Метод executemany
^^^^^^^^^^^^^^^^^

Метод executemany позволяет выполнить одну команду SQL для
последовательности параметров (или для итератора).

С помощью метода executemany в таблицу switch можно добавить аналогичный
список данных одной командой.

Например, в таблицу switch надо добавить данные из списка data2:

.. code:: python

    In [9]: data2 = [
       ...: ('0000.1111.0001', 'sw5', 'Cisco 3750', 'London, Green Str'),
       ...: ('0000.1111.0002', 'sw6', 'Cisco 3750', 'London, Green Str'),
       ...: ('0000.1111.0003', 'sw7', 'Cisco 3750', 'London, Green Str'),
       ...: ('0000.1111.0004', 'sw8', 'Cisco 3750', 'London, Green Str')]

Для этого нужно использовать аналогичный запрос вида:

.. code:: python

    In [10]: query = "INSERT into switch values (?, ?, ?, ?)"

Теперь можно передать данные методу executemany:

.. code:: python

    In [11]: cursor.executemany(query, data2)
    Out[11]: <sqlite3.Cursor at 0x10ee5e810>

    In [12]: connection.commit()

После выполнения commit данные доступны в таблице:

::

    $ litecli sw_inventory.db
    Version: 1.0.0
    Mail: https://groups.google.com/forum/#!forum/litecli-users
    Github: https://github.com/dbcli/litecli
    sw_inventory.db> SELECT * from switch;
    +----------------+----------+------------+-------------------+
    | mac            | hostname | model      | location          |
    +----------------+----------+------------+-------------------+
    | 0000.AAAA.CCCC | sw1      | Cisco 3750 | London, Green Str |
    | 0000.BBBB.CCCC | sw2      | Cisco 3780 | London, Green Str |
    | 0000.AAAA.DDDD | sw3      | Cisco 2960 | London, Green Str |
    | 0011.AAAA.CCCC | sw4      | Cisco 3750 | London, Green Str |
    | 0000.1111.0001 | sw5      | Cisco 3750 | London, Green Str |
    | 0000.1111.0002 | sw6      | Cisco 3750 | London, Green Str |
    | 0000.1111.0003 | sw7      | Cisco 3750 | London, Green Str |
    | 0000.1111.0004 | sw8      | Cisco 3750 | London, Green Str |
    +----------------+----------+------------+-------------------+
    8 rows in set
    Time: 0.034s

Метод executemany подставил соответствующие кортежи в команду SQL, и все
данные добавились в таблицу.

Метод executescript
^^^^^^^^^^^^^^^^^^^

Метод executescript позволяет выполнить несколько выражений SQL за один
раз.

Особенно удобно использовать этот метод при создании таблиц:

.. code:: python

    In [13]: connection = sqlite3.connect('new_db.db')

    In [14]: cursor = connection.cursor()

    In [15]: cursor.executescript('''
        ...:     create table switches(
        ...:         hostname     text not NULL primary key,
        ...:         location     text
        ...:     );
        ...:
        ...:     create table dhcp(
        ...:         mac          text not NULL primary key,
        ...:         ip           text,
        ...:         vlan         text,
        ...:         interface    text,
        ...:         switch       text not null references switches(hostname)
        ...:     );
        ...: ''')
    Out[15]: <sqlite3.Cursor at 0x10efd67a0>

