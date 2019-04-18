WHERE
~~~~~

Оператор WHERE используется для уточнения запроса. С помощью этого
оператора можно указывать определенные условия, по которым отбираются
данные. Если условие выполнено, возвращается соответствующее значение из
таблицы, если нет - не возвращается.

Сейчас в таблице switch всего две записи:

::

    sqlite> SELECT * from switch;
    mac             hostname    model       location
    --------------  ----------  ----------  -----------------
    0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
    0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str

Чтобы в таблице было больше записей, надо создать еще несколько строк. В
SQLite есть метакоманда .read, которая позволяет загружать команды SQL
из файла.

Для добавления записей заготовлен файл add\_rows\_to\_testdb.txt:

.. code:: sql

    INSERT into switch values ('0030.A3AA.C1CC', 'sw3', 'Cisco 3750', 'London, Green Str');
    INSERT into switch values ('0040.A4AA.C2CC', 'sw4', 'Cisco 3850', 'London, Green Str');
    INSERT into switch values ('0050.A5AA.C3CC', 'sw5', 'Cisco 3850', 'London, Green Str');
    INSERT into switch values ('0060.A6AA.C4CC', 'sw6', 'C3750', 'London, Green Str');
    INSERT into switch values ('0070.A7AA.C5CC', 'sw7', 'Cisco 3650', 'London, Green Str');

Для загрузки команд из файла надо выполнить команду:

::

    sqlite> .read add_rows_to_testdb.txt

Теперь таблица switch выглядит так:

.. code:: sql

    sqlite> SELECT * from switch;
    mac             hostname    model       location
    --------------  ----------  ----------  -----------------
    0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
    0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str
    0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str
    0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str
    0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str
    0060.A6AA.C4CC  sw6         C3750       London, Green Str
    0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str

С помощью оператора WHERE можно показать только те коммутаторы, модель
которых 3850:

.. code:: sql

    sqlite> SELECT * from switch WHERE model = 'Cisco 3850';
    mac             hostname    model       location
    --------------  ----------  ----------  -----------------
    0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str
    0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str
    0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str

Оператор WHERE позволяет указывать не только конкретное значение поля.
Если добавить к нему оператор LIKE, можно указывать шаблон поля.

LIKE с помощью символов ``_`` и ``%`` указывает, на что должно быть
похоже значение: \* ``_`` - обозначает один символ или число \* ``%`` -
обозначает ноль, один или много символов

Например, если поле model записано в разном формате, с помощью
предыдущего запроса не получится вывести нужные коммутаторы.

Например, у коммутатора sw6 поле model записано в таком формате: C3750,
а у коммутаторов sw1 и sw3 в таком: Cisco 3750.

В таком варианте запрос с оператором WHERE не покажет sw6:

.. code:: sql

    sqlite> SELECT * from switch WHERE model = 'Cisco 3750';
    mac             hostname    model       location
    --------------  ----------  ----------  -----------------
    0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
    0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str

Но если вместе с оператором WHERE использовать оператор ``LIKE``:

.. code:: sql

    sqlite> SELECT * from switch WHERE model LIKE '%3750';
    mac             hostname    model       location
    --------------  ----------  ----------  -----------------
    0010.A1AA.C1CC  sw1         Cisco 3750  London, Green Str
    0030.A3AA.C1CC  sw3         Cisco 3750  London, Green Str
    0060.A6AA.C4CC  sw6         C3750       London, Green Str

