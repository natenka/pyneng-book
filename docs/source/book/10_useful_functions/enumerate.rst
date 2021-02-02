enumerate
---------

Иногда, при переборе объектов в цикле for, нужно не только получить сам
объект, но и его порядковый номер. Это можно сделать, создав
дополнительную переменную, которая будет расти на единицу с каждым
прохождением цикла. Однако, гораздо удобнее это делать с помощью
итератора ``enumerate``.

Базовый пример:

.. code:: python

    In [15]: list1 = ['str1', 'str2', 'str3']

    In [16]: for position, string in enumerate(list1):
        ...:     print(position, string)
        ...:
    0 str1
    1 str2
    2 str3

``enumerate`` умеет считать не только с нуля, но и с любого значение,
которое ему указали после объекта:

.. code:: python

    In [17]: list1 = ['str1', 'str2', 'str3']

    In [18]: for position, string in enumerate(list1, 100):
        ...:     print(position, string)
        ...:
    100 str1
    101 str2
    102 str3

Иногда нужно проверить, что сгенерировал итератор, как правило, на
стадии написания скрипта. Если необходимо увидеть содержимое, которое
сгенерирует итератор, полностью, можно воспользоваться функцией list:

.. code:: python

    In [19]: list1 = ['str1', 'str2', 'str3']

    In [20]: list(enumerate(list1, 100))
    Out[20]: [(100, 'str1'), (101, 'str2'), (102, 'str3')]

Пример использования enumerate для EEM
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

В этом примере используется Cisco `EEM <http://xgu.ru/wiki/EEM>`__. Если
в двух словах, то EEM позволяет выполнять какие-то действия (action) в
ответ на событие (event).

Выглядит applet EEM так:

.. code:: python

    event manager applet Fa0/1_no_shut
     event syslog pattern "Line protocol on Interface FastEthernet0/0, changed state to down"
     action 1 cli command "enable"
     action 2 cli command "conf t"
     action 3 cli command "interface fa0/1"
     action 4 cli command "no sh"

В EEM, в ситуации, когда действий выполнить нужно много, неудобно каждый
раз набирать ``action x cli command``. Плюс, чаще всего, уже есть
готовый кусок конфигурации, который должен выполнить EEM.

С помощью простого Python-скрипта можно сгенерировать команды EEM на
основании существующего списка команд (файл enumerate_eem.py):

.. code:: python

    import sys

    config = sys.argv[1]

    with open(config, 'r') as f:
        for i, command in enumerate(f, 1):
            print('action {:04} cli command "{}"'.format(i, command.rstrip()))

В данном примере команды считываются из файла, а затем к каждой строке
добавляется приставка, которая нужна для EEM.

Файл с командами выглядит так (r1_config.txt):

.. code:: python

    en
    conf t
    no int Gi0/0/0.300
    no int Gi0/0/0.301
    no int Gi0/0/0.302
    int range gi0/0/0-2
     channel-group 1 mode active
    interface Port-channel1.300
     encapsulation dot1Q 300
     vrf forwarding Management
     ip address 10.16.19.35 255.255.255.248

Вывод будет таким:

.. code:: python

    $ python enumerate_eem.py r1_config.txt
    action 0001 cli command "en"
    action 0002 cli command "conf t"
    action 0003 cli command "no int Gi0/0/0.300"
    action 0004 cli command "no int Gi0/0/0.301"
    action 0005 cli command "no int Gi0/0/0.302"
    action 0006 cli command "int range gi0/0/0-2"
    action 0007 cli command " channel-group 1 mode active"
    action 0008 cli command "interface Port-channel1.300"
    action 0009 cli command " encapsulation dot1Q 300"
    action 0010 cli command " vrf forwarding Management"
    action 0011 cli command " ip address 10.16.19.35 255.255.255.248"

