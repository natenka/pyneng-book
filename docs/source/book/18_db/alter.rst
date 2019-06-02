ALTER
~~~~~

Оператор ALTER позволяет менять существующую таблицу: добавлять новые
колонки или переименовывать таблицу.

Добавим в таблицу новые поля: 

* mngmt_ip - IP-адрес коммутатора в менеджмент VLAN 
* mngmt_vid - VLAN ID (номер VLAN) для менеджмент VLAN

Добавление записей с помощью команды ALTER:

.. code:: sql

    new_db.db> ALTER table switch ADD COLUMN mngmt_ip text;
    You're about to run a destructive command.
    Do you want to proceed? (y/n): y
    Your call!
    Query OK, 0 rows affected
    Time: 0.009s

    new_db.db> ALTER table switch ADD COLUMN mngmt_vid integer;
    You're about to run a destructive command.
    Do you want to proceed? (y/n): y
    Your call!
    Query OK, 0 rows affected
    Time: 0.010s

Теперь таблица выглядит так (новые поля установлены в значение NULL):

.. code:: sql

    new_db.db> SELECT * from switch;
    +----------------+----------+------------+-------------------+----------+-----------+
    | mac            | hostname | model      | location          | mngmt_ip | mngmt_vid |
    +----------------+----------+------------+-------------------+----------+-----------+
    | 0010.A1AA.C1CC | sw1      | Cisco 3750 | London, Green Str | <null>   | <null>    |
    | 0020.A2AA.C2CC | sw2      | Cisco 3850 | London, Green Str | <null>   | <null>    |
    | 0030.A3AA.C1CC | sw3      | Cisco 3750 | London, Green Str | <null>   | <null>    |
    | 0040.A4AA.C2CC | sw4      | Cisco 3850 | London, Green Str | <null>   | <null>    |
    | 0050.A5AA.C3CC | sw5      | Cisco 3850 | London, Green Str | <null>   | <null>    |
    | 0060.A6AA.C4CC | sw6      | C3750      | London, Green Str | <null>   | <null>    |
    | 0070.A7AA.C5CC | sw7      | Cisco 3650 | London, Green Str | <null>   | <null>    |
    +----------------+----------+------------+-------------------+----------+-----------+
    7 rows in set
    Time: 0.034s

