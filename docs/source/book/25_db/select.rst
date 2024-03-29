.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

SELECT
~~~~~~

Оператор SELECT позволяет запрашивать информацию в таблице.

Например:

.. code:: sql

    new_db.db> SELECT * from switch;
    +----------------+----------+------------+-------------------+
    | mac            | hostname | model      | location          |
    +----------------+----------+------------+-------------------+
    | 0010.A1AA.C1CC | sw1      | Cisco 3750 | London, Green Str |
    | 0020.A2AA.C2CC | sw2      | Cisco 3850 | London, Green Str |
    +----------------+----------+------------+-------------------+
    2 rows in set
    Time: 0.033s

``SELECT *`` означает, что нужно вывести все поля таблицы. Следом
указывается, из какой таблицы запрашиваются данные: ``from switch``.

Таким образом можно указывать конкретные столбцы, которые нужно вывести и в каком порядке:

.. code:: sql

    new_db.db> SELECT hostname, mac, model from switch;
    +----------+----------------+------------+
    | hostname | mac            | model      |
    +----------+----------------+------------+
    | sw1      | 0010.A1AA.C1CC | Cisco 3750 |
    | sw2      | 0020.A2AA.C2CC | Cisco 3850 |
    +----------+----------------+------------+
    2 rows in set
    Time: 0.033s
