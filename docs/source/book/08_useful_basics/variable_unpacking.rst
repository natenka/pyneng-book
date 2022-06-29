Распаковка переменных
---------------------

Распаковка переменных - это специальный синтаксис, который позволяет
присваивать переменным элементы итерируемого объекта.

.. note::
    Достаточно часто этот функционал встречается под именем tuple
    unpacking, но распаковка работает на любом итерируемом объекте, не
    только с кортежами

Пример распаковки переменных:

.. code:: python

    In [1]: interface = ['FastEthernet0/1', '10.1.1.1', 'up', 'up']

    In [2]: intf, ip, status, protocol = interface

    In [3]: intf
    Out[3]: 'FastEthernet0/1'

    In [4]: ip
    Out[4]: '10.1.1.1'

Такой вариант намного удобней использовать, чем использование индексов:

.. code:: python

    In [5]: intf, ip, status, protocol = interface[0], interface[1], interface[2], interface[3]

При распаковке переменных каждый элемент списка попадает в
соответствующую переменную. Важно учитывать, что переменных слева
должно быть ровно столько, сколько элементов в списке.

Если переменных больше или меньше, возникнет исключение:

.. code:: python

    In [6]: intf, ip, status = interface
    ------------------------------------------------------------
    ValueError                 Traceback (most recent call last)
    <ipython-input-11-a304c4372b1a> in <module>()
    ----> 1 intf, ip, status = interface

    ValueError: too many values to unpack (expected 3)

    In [7]: intf, ip, status, protocol, other = interface
    ------------------------------------------------------------
    ValueError                 Traceback (most recent call last)
    <ipython-input-12-ac93e78b978c> in <module>()
    ----> 1 intf, ip, status, protocol, other = interface

    ValueError: not enough values to unpack (expected 5, got 4)

Замена ненужных элементов ``_``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Часто из всех элементов итерируемого объекта нужны только
некоторые. При этом синтаксис распаковки требует
указать ровно столько переменных, сколько элементов в итерируемом
объекте.

Если, например, из строки line надо получить только VLAN, MAC и
интерфейс, надо все равно указать переменную для типа записи:

.. code:: python

    In [8]: line = '100    01bb.c580.7000    DYNAMIC     Gi0/1'

    In [9]: vlan, mac, item_type, intf = line.split()

    In [10]: vlan
    Out[10]: '100'

    In [11]: intf
    Out[11]: 'Gi0/1'

Если тип записи не нужен в дальнейшем, можно заменить переменную
item_type нижним подчеркиванием:

.. code:: python

    In [12]: vlan, mac, _, intf = line.split()

Таким образом явно указывается то, что этот элемент не нужен.

Нижнее подчеркивание можно использовать и несколько раз:

.. code:: python

    In [13]: dhcp = '00:09:BB:3D:D6:58   10.1.10.2        86250       dhcp-snooping   10    FastEthernet0/1'

    In [14]: mac, ip, _, _, vlan, intf = dhcp.split()

    In [15]: mac
    Out[15]: '00:09:BB:3D:D6:58'

    In [16]: vlan
    Out[16]: '10'

Использование ``*``
~~~~~~~~~~~~~~~~~~~

Распаковка переменных поддерживает специальный синтаксис, который
позволяет распаковывать несколько элементов в один. Если поставить ``*``
перед именем переменной, в нее запишутся все элементы, кроме тех, что
присвоены явно.

Например, так можно получить первый элемент в переменную first, а
остальные в rest:

.. code:: python

    In [18]: vlans = [10, 11, 13, 30]

    In [19]: first, *rest = vlans

    In [20]: first
    Out[20]: 10

    In [21]: rest
    Out[21]: [11, 13, 30]

При этом переменная со звездочкой всегда будет содержать список:

.. code:: python

    In [22]: vlans = (10, 11, 13, 30)

    In [22]: first, *rest = vlans

    In [23]: first
    Out[23]: 10

    In [24]: rest
    Out[24]: [11, 13, 30]

Если элемент всего один, распаковка все равно отработает:

.. code:: python

    In [25]: first, *rest = vlans

    In [26]: first
    Out[26]: 55

    In [27]: rest
    Out[27]: []

Такая переменная со звездочкой в выражении распаковки может быть только
одна.

.. code:: python

    In [28]: vlans = (10, 11, 13, 30)

    In [29]: first, *rest, *others = vlans
      File "<ipython-input-37-dedf7a08933a>", line 1
        first, *rest, *others = vlans
                                     ^
    SyntaxError: two starred expressions in assignment

Такая переменная может находиться не только в конце выражения:

.. code:: python

    In [30]: vlans = (10, 11, 13, 30)

    In [31]: *rest, last = vlans

    In [32]: rest
    Out[32]: [10, 11, 13]

    In [33]: last
    Out[33]: 30

Таким образом можно указать, что нужен первый, второй и последний
элемент:

.. code:: python

    In [34]: cdp = 'SW1     Eth 0/0    140   S I   WS-C3750-  Eth 0/1'

    In [35]: name, l_intf, *other, r_intf = cdp.split()

    In [36]: name
    Out[36]: 'SW1'

    In [37]: l_intf
    Out[37]: 'Eth'

    In [38]: r_intf
    Out[38]: '0/1'

Примеры распаковки
~~~~~~~~~~~~~~~~~~

Распаковка итерируемых объектов
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Эти примеры показывают, что распаковывать можно не только списки,
кортежи и строки, но и любой другой итерируемый объект.

Распаковка range:

.. code:: python

    In [39]: first, *rest = range(1, 6)

    In [40]: first
    Out[40]: 1

    In [41]: rest
    Out[41]: [2, 3, 4, 5]

Распаковка zip:

.. code:: python

    In [42]: a = [1, 2, 3, 4, 5]

    In [43]: b = [100, 200, 300, 400, 500]

    In [44]: zip(a, b)
    Out[44]: <zip at 0xb4df4fac>

    In [45]: list(zip(a, b))
    Out[45]: [(1, 100), (2, 200), (3, 300), (4, 400), (5, 500)]

    In [46]: first, *rest, last = zip(a, b)

    In [47]: first
    Out[47]: (1, 100)

    In [48]: rest
    Out[48]: [(2, 200), (3, 300), (4, 400)]

    In [49]: last
    Out[49]: (5, 500)

Пример распаковки в цикле for
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Пример цикла, который проходится по ключам:

.. code:: python

    In [50]: access_template = ['switchport mode access',
        ...:                    'switchport access vlan',
        ...:                    'spanning-tree portfast',
        ...:                    'spanning-tree bpduguard enable']
        ...:

    In [51]: access = {'0/12': 10, '0/14': 11, '0/16': 17}

    In [52]: for intf in access:
        ...:     print(f'interface FastEthernet {intf}')
        ...:     for command in access_template:
        ...:         if command.endswith('access vlan'):
        ...:             print(' {} {}'.format(command, access[intf]))
        ...:         else:
        ...:             print(' {}'.format(command))
        ...:
    interface FastEthernet0/12
     switchport mode access
     switchport access vlan 10
     spanning-tree portfast
     spanning-tree bpduguard enable
    interface FastEthernet0/14
     switchport mode access
     switchport access vlan 11
     spanning-tree portfast
     spanning-tree bpduguard enable
    interface FastEthernet0/16
     switchport mode access
     switchport access vlan 17
     spanning-tree portfast
     spanning-tree bpduguard enable

Вместо этого можно проходиться по парам ключ-значение и сразу же
распаковывать их в разные переменные:

.. code:: python

    In [53]: for intf, vlan in access.items():
        ...:     print(f'interface FastEthernet {intf}')
        ...:     for command in access_template:
        ...:         if command.endswith('access vlan'):
        ...:             print(' {} {}'.format(command, vlan))
        ...:         else:
        ...:             print(' {}'.format(command))
        ...:

Пример распаковки элементов списка в цикле:

.. code:: python

    In [54]: table
    Out[54]:
    [['100', 'a1b2.ac10.7000', 'DYNAMIC', 'Gi0/1'],
     ['200', 'a0d4.cb20.7000', 'DYNAMIC', 'Gi0/2'],
     ['300', 'acb4.cd30.7000', 'DYNAMIC', 'Gi0/3'],
     ['100', 'a2bb.ec40.7000', 'DYNAMIC', 'Gi0/4'],
     ['500', 'aa4b.c550.7000', 'DYNAMIC', 'Gi0/5'],
     ['200', 'a1bb.1c60.7000', 'DYNAMIC', 'Gi0/6'],
     ['300', 'aa0b.cc70.7000', 'DYNAMIC', 'Gi0/7']]


    In [55]: for line in table:
        ...:     vlan, mac, _, intf = line
        ...:     print(vlan, mac, intf)
        ...:
    100 a1b2.ac10.7000 Gi0/1
    200 a0d4.cb20.7000 Gi0/2
    300 acb4.cd30.7000 Gi0/3
    100 a2bb.ec40.7000 Gi0/4
    500 aa4b.c550.7000 Gi0/5
    200 a1bb.1c60.7000 Gi0/6
    300 aa0b.cc70.7000 Gi0/7

Но еще лучше сделать так:

.. code:: python

    In [56]: for vlan, mac, _, intf in table:
        ...:     print(vlan, mac, intf)
        ...:
    100 a1b2.ac10.7000 Gi0/1
    200 a0d4.cb20.7000 Gi0/2
    300 acb4.cd30.7000 Gi0/3
    100 a2bb.ec40.7000 Gi0/4
    500 aa4b.c550.7000 Gi0/5
    200 a1bb.1c60.7000 Gi0/6
    300 aa0b.cc70.7000 Gi0/7

