UPDATE
~~~~~~

Оператор UPDATE используется для изменения существующей записи таблицы.

Обычно, UPDATE используется вместе с оператором WHERE, чтобы уточнить,
какую именно запись необходимо изменить.

С помощью UPDATE можно заполнить новые столбцы в таблице.

Например, добавить IP-адрес для коммутатора sw1:

.. code:: sql

    new_db.db> UPDATE switch set mngmt_ip = '10.255.1.1' WHERE hostname = 'sw1';
    Query OK, 1 row affected
    Time: 0.009s

Теперь таблица выглядит так:

.. code:: sql

    new_db.db> SELECT * from switch;
    +----------------+----------+------------+-------------------+------------+-----------+
    | mac            | hostname | model      | location          | mngmt_ip   | mngmt_vid |
    +----------------+----------+------------+-------------------+------------+-----------+
    | 0010.A1AA.C1CC | sw1      | Cisco 3750 | London, Green Str | 10.255.1.1 | <null>    |
    | 0020.A2AA.C2CC | sw2      | Cisco 3850 | London, Green Str | <null>     | <null>    |
    | 0030.A3AA.C1CC | sw3      | Cisco 3750 | London, Green Str | <null>     | <null>    |
    | 0040.A4AA.C2CC | sw4      | Cisco 3850 | London, Green Str | <null>     | <null>    |
    | 0050.A5AA.C3CC | sw5      | Cisco 3850 | London, Green Str | <null>     | <null>    |
    | 0060.A6AA.C4CC | sw6      | C3750      | London, Green Str | <null>     | <null>    |
    | 0070.A7AA.C5CC | sw7      | Cisco 3650 | London, Green Str | <null>     | <null>    |
    +----------------+----------+------------+-------------------+------------+-----------+
    7 rows in set
    Time: 0.035s

Аналогичным образом можно изменить и номер VLAN:

.. code:: sql

    new_db.db> UPDATE switch set mngmt_vid = 255 WHERE hostname = 'sw1';
    Query OK, 1 row affected
    Time: 0.009s

    new_db.db> SELECT * from switch;
    +----------------+----------+------------+-------------------+------------+-----------+
    | mac            | hostname | model      | location          | mngmt_ip   | mngmt_vid |
    +----------------+----------+------------+-------------------+------------+-----------+
    | 0010.A1AA.C1CC | sw1      | Cisco 3750 | London, Green Str | 10.255.1.1 | 255       |
    | 0020.A2AA.C2CC | sw2      | Cisco 3850 | London, Green Str | <null>     | <null>    |
    | 0030.A3AA.C1CC | sw3      | Cisco 3750 | London, Green Str | <null>     | <null>    |
    | 0040.A4AA.C2CC | sw4      | Cisco 3850 | London, Green Str | <null>     | <null>    |
    | 0050.A5AA.C3CC | sw5      | Cisco 3850 | London, Green Str | <null>     | <null>    |
    | 0060.A6AA.C4CC | sw6      | C3750      | London, Green Str | <null>     | <null>    |
    | 0070.A7AA.C5CC | sw7      | Cisco 3650 | London, Green Str | <null>     | <null>    |
    +----------------+----------+------------+-------------------+------------+-----------+
    7 rows in set
    Time: 0.037s


Можно изменить несколько полей за раз:

.. code:: sql

    new_db.db> UPDATE switch set mngmt_ip = '10.255.1.2', mngmt_vid = 255 WHERE hostname = 'sw2'
    Query OK, 1 row affected
    Time: 0.009s

    new_db.db> SELECT * from switch;
    +----------------+----------+------------+-------------------+------------+-----------+
    | mac            | hostname | model      | location          | mngmt_ip   | mngmt_vid |
    +----------------+----------+------------+-------------------+------------+-----------+
    | 0010.A1AA.C1CC | sw1      | Cisco 3750 | London, Green Str | 10.255.1.1 | 255       |
    | 0020.A2AA.C2CC | sw2      | Cisco 3850 | London, Green Str | 10.255.1.2 | 255       |
    | 0030.A3AA.C1CC | sw3      | Cisco 3750 | London, Green Str | <null>     | <null>    |
    | 0040.A4AA.C2CC | sw4      | Cisco 3850 | London, Green Str | <null>     | <null>    |
    | 0050.A5AA.C3CC | sw5      | Cisco 3850 | London, Green Str | <null>     | <null>    |
    | 0060.A6AA.C4CC | sw6      | C3750      | London, Green Str | <null>     | <null>    |
    | 0070.A7AA.C5CC | sw7      | Cisco 3650 | London, Green Str | <null>     | <null>    |
    +----------------+----------+------------+-------------------+------------+-----------+
    7 rows in set
    Time: 0.033s


Чтобы не заполнять поля mngmt_ip и mngmt_vid вручную, заполним
остальное из файла update_fields_in_testdb.txt (команда ``source update_fields_in_testdb.txt``):

::

    UPDATE switch set mngmt_ip = '10.255.1.3', mngmt_vid = 255 WHERE hostname = 'sw3';
    UPDATE switch set mngmt_ip = '10.255.1.4', mngmt_vid = 255 WHERE hostname = 'sw4';
    UPDATE switch set mngmt_ip = '10.255.1.5', mngmt_vid = 255 WHERE hostname = 'sw5';
    UPDATE switch set mngmt_ip = '10.255.1.6', mngmt_vid = 255 WHERE hostname = 'sw6';
    UPDATE switch set mngmt_ip = '10.255.1.7', mngmt_vid = 255 WHERE hostname = 'sw7';

После загрузки команд таблица выглядит так:

.. code:: sql

    new_db.db> SELECT * from switch;
    +----------------+----------+------------+-------------------+------------+-----------+
    | mac            | hostname | model      | location          | mngmt_ip   | mngmt_vid |
    +----------------+----------+------------+-------------------+------------+-----------+
    | 0010.A1AA.C1CC | sw1      | Cisco 3750 | London, Green Str | 10.255.1.1 | 255       |
    | 0020.A2AA.C2CC | sw2      | Cisco 3850 | London, Green Str | 10.255.1.2 | 255       |
    | 0030.A3AA.C1CC | sw3      | Cisco 3750 | London, Green Str | 10.255.1.3 | 255       |
    | 0040.A4AA.C2CC | sw4      | Cisco 3850 | London, Green Str | 10.255.1.4 | 255       |
    | 0050.A5AA.C3CC | sw5      | Cisco 3850 | London, Green Str | 10.255.1.5 | 255       |
    | 0060.A6AA.C4CC | sw6      | C3750      | London, Green Str | 10.255.1.6 | 255       |
    | 0070.A7AA.C5CC | sw7      | Cisco 3650 | London, Green Str | 10.255.1.7 | 255       |
    +----------------+----------+------------+-------------------+------------+-----------+
    7 rows in set
    Time: 0.038s

Теперь предположим, что sw1 был заменен с модели 3750 на модель 3850.
Соответственно, изменилось не только поле модель, но и поле MAC-адрес.

Внесение изменений:

.. code:: sql

    new_db.db> UPDATE switch set model = 'Cisco 3850', mac = '0010.D1DD.E1EE' WHERE hostname = 'sw1';
    Query OK, 1 row affected
    Time: 0.009s

Результат будет таким:

.. code:: sql

    new_db.db> SELECT * from switch;
    +----------------+----------+------------+-------------------+------------+-----------+
    | mac            | hostname | model      | location          | mngmt_ip   | mngmt_vid |
    +----------------+----------+------------+-------------------+------------+-----------+
    | 0010.D1DD.E1EE | sw1      | Cisco 3850 | London, Green Str | 10.255.1.1 | 255       |
    | 0020.A2AA.C2CC | sw2      | Cisco 3850 | London, Green Str | 10.255.1.2 | 255       |
    | 0030.A3AA.C1CC | sw3      | Cisco 3750 | London, Green Str | 10.255.1.3 | 255       |
    | 0040.A4AA.C2CC | sw4      | Cisco 3850 | London, Green Str | 10.255.1.4 | 255       |
    | 0050.A5AA.C3CC | sw5      | Cisco 3850 | London, Green Str | 10.255.1.5 | 255       |
    | 0060.A6AA.C4CC | sw6      | C3750      | London, Green Str | 10.255.1.6 | 255       |
    | 0070.A7AA.C5CC | sw7      | Cisco 3650 | London, Green Str | 10.255.1.7 | 255       |
    +----------------+----------+------------+-------------------+------------+-----------+
    7 rows in set
    Time: 0.049s

