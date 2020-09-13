DELETE
~~~~~~

Оператор DELETE используется для удаления записей.
Как правило, он используется вместе с оператором WHERE.

Например, таблица switch выглядит так:

.. code:: sql

    new_db.db> SELECT * from switch;
    +----------------+----------+------------+-------------------+------------+-----------+
    | mac            | hostname | model      | location          | mngmt_ip   | mngmt_vid |
    +----------------+----------+------------+-------------------+------------+-----------+
    | 0010.D1DD.E1EE | sw1      | Cisco 3850 | London, Green Str | 10.255.1.1 | 255       |
    | 0020.A2AA.C2CC | sw2      | Cisco 3850 | London, Green Str | 10.255.1.2 | 255       |
    | 0040.A4AA.C2CC | sw4      | Cisco 3850 | London, Green Str | 10.255.1.4 | 255       |
    | 0050.A5AA.C3CC | sw5      | Cisco 3850 | London, Green Str | 10.255.1.5 | 255       |
    | 0060.A6AA.C4CC | sw6      | C3750      | London, Green Str | 10.255.1.6 | 255       |
    | 0070.A7AA.C5CC | sw7      | Cisco 3650 | London, Green Str | 10.255.1.7 | 255       |
    | 0030.A3AA.C1CC | sw3      | Cisco 3850 | London, Green Str | 10.255.1.3 | 255       |
    | 0080.A8AA.C8CC | sw8      | Cisco 3850 | London, Green Str | 10.255.1.8 | 255       |
    +----------------+----------+------------+-------------------+------------+-----------+
    8 rows in set
    Time: 0.033s

Удаление информации про коммутатор sw8 выполняется таким образом:

.. code:: sql

    new_db.db> DELETE from switch where hostname = 'sw8';
    You're about to run a destructive command.
    Do you want to proceed? (y/n): y
    Your call!
    Query OK, 1 row affected
    Time: 0.008s

Теперь в таблице нет строки с коммутатором sw8:

.. code:: sql

    new_db.db> SELECT * from switch;
    +----------------+----------+------------+-------------------+------------+-----------+
    | mac            | hostname | model      | location          | mngmt_ip   | mngmt_vid |
    +----------------+----------+------------+-------------------+------------+-----------+
    | 0010.D1DD.E1EE | sw1      | Cisco 3850 | London, Green Str | 10.255.1.1 | 255       |
    | 0020.A2AA.C2CC | sw2      | Cisco 3850 | London, Green Str | 10.255.1.2 | 255       |
    | 0040.A4AA.C2CC | sw4      | Cisco 3850 | London, Green Str | 10.255.1.4 | 255       |
    | 0050.A5AA.C3CC | sw5      | Cisco 3850 | London, Green Str | 10.255.1.5 | 255       |
    | 0060.A6AA.C4CC | sw6      | C3750      | London, Green Str | 10.255.1.6 | 255       |
    | 0070.A7AA.C5CC | sw7      | Cisco 3650 | London, Green Str | 10.255.1.7 | 255       |
    | 0030.A3AA.C1CC | sw3      | Cisco 3850 | London, Green Str | 10.255.1.3 | 255       |
    +----------------+----------+------------+-------------------+------------+-----------+
    7 rows in set
    Time: 0.039s

