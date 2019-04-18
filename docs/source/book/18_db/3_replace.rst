REPLACE
~~~~~~~

Оператор REPLACE используется для добавления или замены данных в
таблице.

    Оператор REPLACE может поддерживаться не во всех СУБД.

Когда возникает нарушение условия уникальности поля, выражение с
оператором REPLACE: \* удаляет существующую строку, которая вызвала
нарушение \* добавляет новую строку

У выражения REPLACE есть два вида:

::

    sqlite> INSERT OR REPLACE INTO switch
       ...> VALUES ('0030.A3AA.C1CC', 'sw3', 'Cisco 3850', 'London, Green Str', '10.255.1.3', 255);

Или более короткий вариант:

::

    sqlite> REPLACE INTO switch
       ...> VALUES ('0030.A3AA.C1CC', 'sw3', 'Cisco 3850', 'London, Green Str', '10.255.1.3', 255);

Результатом любой из этих команд будет замена модели коммутатора sw3:

.. code:: sql

    sqlite> SELECT * from switch;
    mac             hostname    model       location           mngmt_ip    mngmt_vid
    --------------  ----------  ----------  -----------------  ----------  ----------
    0010.D1DD.E1EE  sw1         Cisco 3850  London, Green Str  10.255.1.1  255
    0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str  10.255.1.2  255
    0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
    0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
    0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255
    0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str  10.255.1.7  255
    0030.A3AA.C1CC  sw3         Cisco 3850  London, Green Str  10.255.1.3  255

В данном случае MAC-адрес в новой записи совпадает с уже существующей,
поэтому происходит замена.

    Если были указаны не все поля, в новой записи будут только те поля,
    которые были указаны. Это связано с тем, что replace сначала удаляет
    существующую запись.

При добавлении записи, для которой не возникает нарушения уникальности
поля, replace работает как обычный insert:

::

    sqlite> REPLACE INTO switch
       ...> VALUES ('0080.A8AA.C8CC', 'sw8', 'Cisco 3850', 'London, Green Str', '10.255.1.8', 255);

    sqlite> SELECT * from switch;
    mac             hostname    model       location           mngmt_ip    mngmt_vid
    --------------  ----------  ----------  -----------------  ----------  ----------
    0010.D1DD.E1EE  sw1         Cisco 3850  London, Green Str  10.255.1.1  255
    0020.A2AA.C2CC  sw2         Cisco 3850  London, Green Str  10.255.1.2  255
    0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
    0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
    0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255
    0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str  10.255.1.7  255
    0030.A3AA.C1CC  sw3         Cisco 3850  London, Green Str  10.255.1.3  255
    0080.A8AA.C8CC  sw8         Cisco 3850  London, Green Str  10.255.1.8  255

