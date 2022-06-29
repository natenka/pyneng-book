
.. _x_comprehensions:

List, dict, set comprehensions
==============================

Python поддерживает специальные выражения, которые позволяют компактно
создавать списки, словари и множества.

На английском эти выражения называются, соответственно:

-  List comprehensions
-  Dict comprehensions
-  Set comprehensions

К сожалению, официальный перевод на русский звучит как `абстракция
списков или списковое
включение <https://ru.wikipedia.org/wiki/%D0%A1%D0%BF%D0%B8%D1%81%D0%BA%D0%BE%D0%B2%D0%BE%D0%B5_%D0%B2%D0%BA%D0%BB%D1%8E%D1%87%D0%B5%D0%BD%D0%B8%D0%B5>`__,
что не особо помогает понять суть объекта.

В книге использовался перевод "генератор списка", что, к сожалению, тоже
не самый удачный вариант, так как в Python есть отдельное понятие
генератор и генераторные выражения, но он лучше отображает суть
выражения.

Эти выражения не только позволяют более компактно создавать
соответствующие объекты, но и создают их быстрее. И хотя поначалу они
требуют определенной привычки использования и понимания, они очень часто
используются.

List comprehensions (генераторы списков)
----------------------------------------

Генератор списка - это выражение вида:

.. code:: python

    In [1]: vlans = [f'vlan {num}' for num in range(10,16)]

    In [2]: print(vlans)
    ['vlan 10', 'vlan 11', 'vlan 12', 'vlan 13', 'vlan 14', 'vlan 15']

В общем случае, это выражение, которое преобразует итерируемый объект в
список. То есть, последовательность элементов преобразуется и
добавляется в новый список.

Выражению выше аналогичен такой цикл:

.. code:: python

    In [3]: vlans = []

    In [4]: for num in range(10,16):
       ...:     vlans.append(f'vlan {num}')
       ...:

    In [5]: print(vlans)
    ['vlan 10', 'vlan 11', 'vlan 12', 'vlan 13', 'vlan 14', 'vlan 15']

В list comprehensions можно использовать выражение if. Таким образом
можно добавлять в список только некоторые объекты.

Например, такой цикл отбирает те элементы, которые являются числами,
конвертирует их и добавляет в итоговый список only_digits:

.. code:: python

    In [6]: items = ['10', '20', 'a', '30', 'b', '40']

    In [7]: only_digits = []

    In [8]: for item in items:
       ...:     if item.isdigit():
       ...:         only_digits.append(int(item))
       ...:

    In [9]: print(only_digits)
    [10, 20, 30, 40]

Аналогичный вариант в виде list comprehensions:

.. code:: python

    In [10]: items = ['10', '20', 'a', '30', 'b', '40']

    In [11]: only_digits = [int(item) for item in items if item.isdigit()]

    In [12]: print(only_digits)
    [10, 20, 30, 40]

Конечно, далеко не все циклы можно переписать как генератор списка, но
когда это можно сделать, и при этом выражение не усложняется, лучше
использовать генераторы списка.

.. note::
    В Python генераторы списка могут также заменить функции filter и map
    и считаются более понятными вариантами решения.

С помощью генератора списка также удобно получать элементы из вложенных
словарей:

.. code:: python

    In [13]: london_co = {
        ...:     'r1' : {
        ...:     'hostname': 'london_r1',
        ...:     'location': '21 New Globe Walk',
        ...:     'vendor': 'Cisco',
        ...:     'model': '4451',
        ...:     'IOS': '15.4',
        ...:     'IP': '10.255.0.1'
        ...:     },
        ...:     'r2' : {
        ...:     'hostname': 'london_r2',
        ...:     'location': '21 New Globe Walk',
        ...:     'vendor': 'Cisco',
        ...:     'model': '4451',
        ...:     'IOS': '15.4',
        ...:     'IP': '10.255.0.2'
        ...:     },
        ...:     'sw1' : {
        ...:     'hostname': 'london_sw1',
        ...:     'location': '21 New Globe Walk',
        ...:     'vendor': 'Cisco',
        ...:     'model': '3850',
        ...:     'IOS': '3.6.XE',
        ...:     'IP': '10.255.0.101'
        ...:     }
        ...: }

    In [14]: [london_co[device]['IOS'] for device in london_co]
    Out[14]: ['15.4', '15.4', '3.6.XE']

    In [15]: [london_co[device]['IP'] for device in london_co]
    Out[15]: ['10.255.0.1', '10.255.0.2', '10.255.0.101']

На самом деле, синтаксис генератора списка выглядит так:

.. code:: python

    [expression for item1 in iterable1 if condition1 
                for item2 in iterable2 if condition2
                ...
                for itemN in iterableN if conditionN ]

Это значит, можно использовать несколько for в выражении.

Например, в списке vlans находятся несколько вложенных списков с
VLAN'ами:

.. code:: python

    In [16]: vlans = [[10,21,35], [101, 115, 150], [111, 40, 50]]

Из этого списка надо сформировать один плоский список с номерами VLAN.
Первый вариант — с помощью циклов for:

.. code:: python

    In [17]: result = []

    In [18]: for vlan_list in vlans:
        ...:     for vlan in vlan_list:
        ...:         result.append(vlan)
        ...:

    In [19]: print(result)
    [10, 21, 35, 101, 115, 150, 111, 40, 50]

Аналогичный вариант с генератором списков:

.. code:: python

    In [20]: vlans = [[10,21,35], [101, 115, 150], [111, 40, 50]]

    In [21]: result = [vlan for vlan_list in vlans for vlan in vlan_list]

    In [22]: print(result)
    [10, 21, 35, 101, 115, 150, 111, 40, 50]

Можно одновременно проходиться по двум последовательностям, используя
zip:

.. code:: python

    In [23]: vlans = [100, 110, 150, 200]

    In [24]: names = ['mngmt', 'voice', 'video', 'dmz']

    In [25]: result = ['vlan {}\n name {}'.format(vlan, name) for vlan, name in zip(vlans, names)]

    In [26]: print('\n'.join(result))
    vlan 100
     name mngmt
    vlan 110
     name voice
    vlan 150
     name video
    vlan 200
     name dmz

Dict comprehensions (генераторы словарей)
-----------------------------------------

Генераторы словарей аналогичны генераторам списков, но они используются
для создания словарей.

Например, такое выражение:

.. code:: python

    In [27]: d = {}

    In [28]: for num in range(1, 11):
        ...:     d[num] = num**2
        ...:

    In [29]: print(d)
    {1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81, 10: 100}

Можно заменить генератором словаря:

.. code:: python

    In [30]: d = {num: num**2 for num in range(1, 11)}

    In [31]: print(d)
    {1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81, 10: 100}

Еще один пример, в котором надо преобразовать существующий словарь и
перевести все ключи в нижний регистр. Для начала, вариант решения без
генератора словаря:

.. code:: python

    In [32]: r1 = {'IOS': '15.4',
        ...:       'IP': '10.255.0.1',
        ...:       'hostname': 'london_r1',
        ...:       'location': '21 New Globe Walk',
        ...:       'model': '4451',
        ...:       'vendor': 'Cisco'}
        ...:

    In [33]: lower_r1 = {}

    In [34]: for key, value in r1.items():
        ...:     lower_r1[key.lower()] = value
        ...:

    In [35]: lower_r1
    Out[35]:
    {'hostname': 'london_r1',
     'ios': '15.4',
     'ip': '10.255.0.1',
     'location': '21 New Globe Walk',
     'model': '4451',
     'vendor': 'Cisco'}

Аналогичный вариант с помощью генератора словаря:

.. code:: python

    In [36]: r1 = {'IOS': '15.4',
        ...:   'IP': '10.255.0.1',
        ...:   'hostname': 'london_r1',
        ...:   'location': '21 New Globe Walk',
        ...:   'model': '4451',
        ...:   'vendor': 'Cisco'}
        ...:

    In [37]: lower_r1 = {key.lower(): value for key, value in r1.items()}

    In [38]: lower_r1
    Out[38]:
    {'hostname': 'london_r1',
     'ios': '15.4',
     'ip': '10.255.0.1',
     'location': '21 New Globe Walk',
     'model': '4451',
     'vendor': 'Cisco'}

Как и list comprehensions, dict comprehensions можно делать вложенными.
Попробуем аналогичным образом преобразовать ключи во вложенных словарях:

.. code:: python

    In [39]: london_co = {
        ...:     'r1' : {
        ...:     'hostname': 'london_r1',
        ...:     'location': '21 New Globe Walk',
        ...:     'vendor': 'Cisco',
        ...:     'model': '4451',
        ...:     'IOS': '15.4',
        ...:     'IP': '10.255.0.1'
        ...:     },
        ...:     'r2' : {
        ...:     'hostname': 'london_r2',
        ...:     'location': '21 New Globe Walk',
        ...:     'vendor': 'Cisco',
        ...:     'model': '4451',
        ...:     'IOS': '15.4',
        ...:     'IP': '10.255.0.2'
        ...:     },
        ...:     'sw1' : {
        ...:     'hostname': 'london_sw1',
        ...:     'location': '21 New Globe Walk',
        ...:     'vendor': 'Cisco',
        ...:     'model': '3850',
        ...:     'IOS': '3.6.XE',
        ...:     'IP': '10.255.0.101'
        ...:     }
        ...: }

    In [40]: lower_london_co = {}

    In [41]: for device, params in london_co.items():
        ...:     lower_london_co[device] = {}
        ...:     for key, value in params.items():
        ...:         lower_london_co[device][key.lower()] = value
        ...:

    In [42]: lower_london_co
    Out[42]:
    {'r1': {'hostname': 'london_r1',
      'ios': '15.4',
      'ip': '10.255.0.1',
      'location': '21 New Globe Walk',
      'model': '4451',
      'vendor': 'Cisco'},
     'r2': {'hostname': 'london_r2',
      'ios': '15.4',
      'ip': '10.255.0.2',
      'location': '21 New Globe Walk',
      'model': '4451',
      'vendor': 'Cisco'},
     'sw1': {'hostname': 'london_sw1',
      'ios': '3.6.XE',
      'ip': '10.255.0.101',
      'location': '21 New Globe Walk',
      'model': '3850',
      'vendor': 'Cisco'}}

Аналогичное преобразование с dict comprehensions:

.. code:: python

    In [43]: result = {device: {key.lower(): value for key, value in params.items()} for device, params in london_co.items()}

    In [44]: result
    Out[44]:
    {'r1': {'hostname': 'london_r1',
      'ios': '15.4',
      'ip': '10.255.0.1',
      'location': '21 New Globe Walk',
      'model': '4451',
      'vendor': 'Cisco'},
     'r2': {'hostname': 'london_r2',
      'ios': '15.4',
      'ip': '10.255.0.2',
      'location': '21 New Globe Walk',
      'model': '4451',
      'vendor': 'Cisco'},
     'sw1': {'hostname': 'london_sw1',
      'ios': '3.6.XE',
      'ip': '10.255.0.101',
      'location': '21 New Globe Walk',
      'model': '3850',
      'vendor': 'Cisco'}}

Set comprehensions (генераторы множеств)
----------------------------------------

Генераторы множеств в целом аналогичны генераторам списков.

Например, надо получить множество с уникальными номерами VLAN'ов:

.. code:: python

    In [45]: vlans = [10, '30', 30, 10, '56']

    In [46]: unique_vlans = {int(vlan) for vlan in vlans}

    In [47]: unique_vlans
    Out[47]: {10, 30, 56}

Аналогичное решение, без использования set comprehensions:

.. code:: python

    In [48]: vlans = [10, '30', 30, 10, '56']

    In [49]: unique_vlans = set()

    In [50]: for vlan in vlans:
        ...:     unique_vlans.add(int(vlan))
        ...:

    In [51]: unique_vlans
    Out[51]: {10, 30, 56}

