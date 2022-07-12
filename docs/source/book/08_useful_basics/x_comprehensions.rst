
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

Генератор списка (list comprehensions или list comp) - это выражение вида:

.. code:: python

    vlans = [int(vl) for vl in items]

Список items:

.. code:: python

    items = ["10", "20", "30", "1", "11", "100"]

В общем случае, list comprehension это выражение, которое преобразует
итерируемый объект в список. То есть, последовательность элементов
преобразуется и добавляется в новый список.

List comp выше аналогичен такой цикл:

.. code:: python

    items = ["10", "20", "30", "1", "11", "100"]

    vlans = []
    for vl in items:
        vlans.append(int(vl))

    print(vlans)
    # [10, 20, 30, 1, 11, 100]

Соответствие между обычным циклом и генератором списка:

.. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/08_list_comp.png
   :align: center
   :class: only-light


.. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/08_list_comp.png
   :align: center
   :class: only-dark

В list comprehensions можно использовать выражение if. Таким образом
можно добавлять в список только некоторые объекты.

Например, такой цикл отбирает те элементы, которые являются числами,
конвертирует их и добавляет в итоговый список only_digits:

.. code:: python

    items = ['10', '20', 'a', '30', 'b', '40']

    only_digits = []

    for item in items:
        if item.isdigit():
            only_digits.append(int(item))

    In [9]: print(only_digits)
    [10, 20, 30, 40]

Аналогичный вариант в виде list comprehensions:

.. code:: python

    items = ['10', '20', 'a', '30', 'b', '40']
    only_digits = [int(item) for item in items if item.isdigit()]

    In [12]: print(only_digits)
    [10, 20, 30, 40]

Соответствие между циклом с условием и генератором списка с условием:

.. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/08_list_comp_if.png
   :align: center
   :class: only-light


.. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/08_list_comp_if_dark.png
   :align: center
   :class: only-dark

Конечно, далеко не все циклы можно переписать как генератор списка, но
когда это можно сделать, и при этом выражение не усложняется, лучше
использовать генераторы списка.

.. note::

    В Python генераторы списка могут также заменить функции filter и map
    и считаются более понятными вариантами решения.

С помощью генератора списка также удобно получать элементы из вложенных
словарей:

.. code:: python

    london_co = {
        'r1' : {
        'hostname': 'london_r1',
        'location': '21 New Globe Walk',
        'vendor': 'Cisco',
        'model': '4451',
        'ios': '15.4',
        'ip': '10.255.0.1'
        },
        'r2' : {
        'hostname': 'london_r2',
        'location': '21 New Globe Walk',
        'vendor': 'Cisco',
        'model': '4451',
        'ios': '15.4',
        'ip': '10.255.0.2'
        },
        'sw1' : {
        'hostname': 'london_sw1',
        'location': '21 New Globe Walk',
        'vendor': 'Cisco',
        'model': '3850',
        'ios': '3.6.XE',
        'ip': '10.255.0.101'
        }
    }

    In [14]: [london_co[device]['ios'] for device in london_co]
    Out[14]: ['15.4', '15.4', '3.6.XE']

    In [15]: [london_co[device]['ip'] for device in london_co]
    Out[15]: ['10.255.0.1', '10.255.0.2', '10.255.0.101']

Полный синтаксис генератора списка выглядит так:

.. code:: python

    [expression for item1 in iterable1 if condition1
                for item2 in iterable2 if condition2
                ...
                for itemN in iterableN if conditionN ]

Это значит, можно использовать несколько for в выражении.

Например, в списке vlans находятся несколько вложенных списков с
VLAN'ами:

.. code:: python

    vlans = [[10, 21, 35], [101, 115, 150], [111, 40, 50]]

Из этого списка надо сформировать один плоский список с номерами VLAN.
Первый вариант — с помощью циклов for:

.. code:: python

    result = []

    for vlan_list in vlans:
        for vlan in vlan_list:
            result.append(vlan)


    In [19]: print(result)
    [10, 21, 35, 101, 115, 150, 111, 40, 50]

Аналогичный вариант с генератором списков:

.. code:: python

    vlans = [[10, 21, 35], [101, 115, 150], [111, 40, 50]]
    result = [vlan for vlan_list in vlans for vlan in vlan_list]

    In [22]: print(result)
    [10, 21, 35, 101, 115, 150, 111, 40, 50]

Соответствие между двумя вложенными циклами и генератором списка с двумя циклами:

.. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/08_list_comp_for_for.png
   :align: center
   :class: only-light


.. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/08_list_comp_for_for_dark.png
   :align: center
   :class: only-dark

Можно одновременно проходиться по двум последовательностям, используя
zip:

.. code:: python

    vlans = [100, 110, 150, 200]
    names = ['mngmt', 'voice', 'video', 'dmz']

    result = ['vlan {}\n name {}'.format(vlan, name) for vlan, name in zip(vlans, names)]

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

    d = {}

    for num in range(1, 11):
        d[num] = num**2

    In [29]: print(d)
    {1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81, 10: 100}

Можно заменить генератором словаря:

.. code:: python

    d = {num: num**2 for num in range(1, 11)}

    In [31]: print(d)
    {1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81, 10: 100}

Еще один пример, в котором надо преобразовать существующий словарь и
перевести все ключи в нижний регистр. Для начала, вариант решения без
генератора словаря:

.. code:: python

    r1 = {'ios': '15.4',
          'ip': '10.255.0.1',
          'hostname': 'london_r1',
          'location': '21 New Globe Walk',
          'model': '4451',
          'vendor': 'Cisco'}

    lower_r1 = {}

    for key, value in r1.items():
        lower_r1[key.lower()] = value

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

    r1 = {'ios': '15.4',
      'ip': '10.255.0.1',
      'hostname': 'london_r1',
      'location': '21 New Globe Walk',
      'model': '4451',
      'vendor': 'Cisco'}

    lower_r1 = {key.lower(): value for key, value in r1.items()}

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

    london_co = {
        'r1' : {
        'hostname': 'london_r1',
        'location': '21 New Globe Walk',
        'vendor': 'Cisco',
        'model': '4451',
        'ios': '15.4',
        'ip': '10.255.0.1'
        },
        'r2' : {
        'hostname': 'london_r2',
        'location': '21 New Globe Walk',
        'vendor': 'Cisco',
        'model': '4451',
        'ios': '15.4',
        'ip': '10.255.0.2'
        },
        'sw1' : {
        'hostname': 'london_sw1',
        'location': '21 New Globe Walk',
        'vendor': 'Cisco',
        'model': '3850',
        'ios': '3.6.XE',
        'ip': '10.255.0.101'
        }
    }

    lower_london_co = {}

    for device, params in london_co.items():
        lower_london_co[device] = {}
        for key, value in params.items():
            lower_london_co[device][key.lower()] = value

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

    result = {device: {key.lower(): value for key, value in params.items()}
              for device, params in london_co.items()}

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

    vlans = [10, '30', 30, 10, '56']

    unique_vlans = {int(vlan) for vlan in vlans}

    In [47]: unique_vlans
    Out[47]: {10, 30, 56}

Аналогичное решение, без использования set comprehensions:

.. code:: python

    vlans = [10, '30', 30, 10, '56']

    unique_vlans = set()

    for vlan in vlans:
        unique_vlans.add(int(vlan))

    In [51]: unique_vlans
    Out[51]: {10, 30, 56}

