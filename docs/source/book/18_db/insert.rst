INSERT
~~~~~~

Оператор INSERT используется для добавления данных в таблицу.

.. note::

    Если таблица была удалена на предыдущем шаге, надо ее создать:

    .. code:: sql

        new_db.db> create table switch (mac text not NULL primary key, hostname text, model text, location text);
        Query OK, 0 rows affected
        Time: 0.010s

Есть несколько вариантов добавления записей, в зависимости от того, все
ли поля будут заполнены, и будут ли они идти по порядку определения
полей или нет.

Если указываются значения для всех полей, добавить запись можно таким
образом (порядок полей должен соблюдаться):

.. code:: sql

    new_db.db> INSERT into switch values ('0010.A1AA.C1CC', 'sw1', 'Cisco 3750', 'London, Green Str');
    Query OK, 1 row affected
    Time: 0.008s

Если нужно указать не все поля или указать их в произвольном порядке,
используется такая запись:

.. code:: sql

    new_db.db> INSERT into switch (mac, model, location, hostname) values ('0020.A2AA.C2CC', 'Cisco 3850', 'London, Green Str', 'sw2');
    Query OK, 1 row affected
    Time: 0.009s

