CREATE
~~~~~~

Оператор create позволяет создавать таблицы.

Создадим таблицу switch, в которой хранится информация о коммутаторах:

.. code:: sql

    sqlite> CREATE table switch (
       ...>     mac          text not NULL primary key,
       ...>     hostname     text,
       ...>     model        text,
       ...>     location     text
       ...> );

Аналогично можно было создать таблицу и таким образом:

.. code:: sql

    sqlite> create table switch (mac text not NULL primary key, hostname text, model text, location text);

В данном примере мы описали таблицу switch: определили, какие поля будут
в таблице, и значения какого типа будут в них находиться.

Кроме того, поле mac является первичным ключом. Это автоматически
значит, что: \* поле должно быть уникальным \* в нём не может находиться
значение NULL (в SQLite это надо задавать явно)

В этом примере это вполне логично, так как MAC-адрес должен быть
уникальным.

На данный момент записей в таблице нет, есть только ее определение.
Просмотреть определение можно такой командой:

.. code:: sql

    sqlite> .schema switch
    CREATE TABLE switch (
    mac          text not NULL primary key,
    hostname     text,
    model        text,
    location     text
    );

