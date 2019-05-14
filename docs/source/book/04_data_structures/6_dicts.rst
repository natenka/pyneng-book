Словарь (Dictionary)
====================

Словари - это изменяемый неупорядоченный тип данных

    В модуле
    `collections <https://docs.python.org/3/library/collections.html>`__
    доступны упорядоченные объекты, внешне идентичные словарям
    `OrderedDict <https://docs.python.org/3/library/collections.html#collections.OrderedDict>`__.

Словарь (ассоциативный массив, хеш-таблица): \* данные в словаре - это
пары ``ключ: значение`` \* доступ к значениям осуществляется по ключу, а
не по номеру, как в списках \* словари не упорядочены, поэтому не стоит
полагаться на порядок элементов словаря \* так как словари изменяемы, то
элементы словаря можно менять, добавлять, удалять \* ключ должен быть
объектом неизменяемого типа: \* число \* строка \* кортеж \* значение
может быть данными любого типа

Пример словаря:

.. code:: python

    london = {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco', 'model': '4451', 'ios': '15.4'}

Можно записывать и так:

.. code:: python

    london = {
            'id': 1,
            'name':'London',
            'it_vlan':320,
            'user_vlan':1010,
            'mngmt_vlan':99,
            'to_name': None,
            'to_id': None,
            'port':'G1/0/11'
    }

Для того, чтобы получить значение из словаря, надо обратиться по ключу,
таким же образом, как это было в списках, только вместо номера будет
использоваться ключ:

.. code:: python

    In [1]: london = {'name': 'London1', 'location': 'London Str'}

    In [2]: london['name']
    Out[2]: 'London1'

    In [3]: london['location']
    Out[3]: 'London Str'

Аналогичным образом можно добавить новую пару ключ:значение:

.. code:: python

    In [4]: london['vendor'] = 'Cisco'

    In [5]: print(london)
    {'vendor': 'Cisco', 'name': 'London1', 'location': 'London Str'}

В словаре в качестве значения можно использовать словарь:

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

Получить значения из вложенного словаря можно так:

.. code:: python

    In [7]: london_co['r1']['ios']
    Out[7]: '15.4'

    In [8]: london_co['r1']['model']
    Out[8]: '4451'

    In [9]: london_co['sw1']['ip']
    Out[9]: '10.255.0.101'

.. toctree::
   :maxdepth: 1

   6a_dict_methods
   6b_create_dict
