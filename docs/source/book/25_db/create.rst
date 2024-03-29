.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

CREATE
~~~~~~

Оператор CREATE позволяет создавать таблицы.

Сначала подключимся к базе данных или создадим ее с помощью litecli:

::

    $ litecli new_db.db
    Version: 1.0.0
    Mail: https://groups.google.com/forum/#!forum/litecli-users
    Github: https://github.com/dbcli/litecli
    new_db.db>


Создадим таблицу switch, в которой хранится информация о коммутаторах:

.. code:: sql

    new_db.db> create table switch (mac text not NULL primary key, hostname text, model text, location text);
    Query OK, 0 rows affected
    Time: 0.010s


В данном примере мы описали таблицу switch: определили, какие поля будут
в таблице, и значения какого типа будут в них находиться.

Кроме того, поле mac является первичным ключом. Это автоматически
значит, что: 

* поле должно быть уникальным 
* в нём не может находиться значение NULL (в SQLite это надо задавать явно)

В этом примере это вполне логично, так как MAC-адрес должен быть
уникальным.

На данный момент записей в таблице нет, есть только ее определение.
Просмотреть определение можно такой командой:

.. code:: sql

    new_db.db> .schema switch
    +-----------------------------------------------------------------------------------------------+
    | sql                                                                                           |
    +-----------------------------------------------------------------------------------------------+
    | CREATE TABLE switch (mac text not NULL primary key, hostname text, model text, location text) |
    +-----------------------------------------------------------------------------------------------+
    Time: 0.037s
