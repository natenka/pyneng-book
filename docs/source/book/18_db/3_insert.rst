INSERT
~~~~~~

Оператор insert используется для добавления данных в таблицу.

Есть несколько вариантов добавления записей, в зависимости от того, все
ли поля будут заполнены, и будут ли они идти по порядку определения
полей или нет.

    Если Вы удалили таблицу, выполнив drop, надо ее заново создать:
    ``create table switch (mac text not NULL primary key, hostname text, model text, location text);``

Если указываются значения для всех полей, добавить запись можно таким
образом (порядок полей должен соблюдаться):

.. code:: sql

    sqlite> INSERT into switch values ('0010.A1AA.C1CC', 'sw1', 'Cisco 3750', 'London, Green Str');

Если нужно указать не все поля или указать их в произвольном порядке,
используется такая запись:

.. code:: sql

    sqlite> INSERT into switch (mac, model, location, hostname)
       ...> values ('0020.A2AA.C2CC', 'Cisco 3850', 'London, Green Str', 'sw2');

