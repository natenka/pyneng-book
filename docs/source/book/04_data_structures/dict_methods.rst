Полезные методы для работы со словарями
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``clear``
^^^^^^^^^^^

Метод ``clear`` позволяет очистить словарь:

.. code:: python

    In [1]: london = {'name': 'London1', 'location': 'London Str'}

    In [2]: london.clear()

    In [3]: london
    Out[3]: {}

``copy``
^^^^^^^^^^

Метод ``copy`` позволяет создать полную копию словаря.

Если указать, что один словарь равен другому:

.. code:: python

    In [4]: london = {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco'}

    In [5]: london2 = london

    In [6]: id(london)
    Out[6]: 25489072

    In [7]: id(london2)
    Out[7]: 25489072

    In [8]: london['vendor'] = 'Juniper'

    In [9]: london2['vendor']
    Out[9]: 'Juniper'

В этом случае london2 это еще одно имя, которое ссылается на словарь. И
при изменениях словаря london меняется и словарь london2, так как это
ссылки на один и тот же объект.

Поэтому, если нужно сделать копию словаря, надо использовать метод
copy():

.. code:: python

    In [10]: london = {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco'}

    In [11]: london2 = london.copy()

    In [12]: id(london)
    Out[12]: 25524512

    In [13]: id(london2)
    Out[13]: 25563296

    In [14]: london['vendor'] = 'Juniper'

    In [15]: london2['vendor']
    Out[15]: 'Cisco'

``get``
^^^^^^^^^

Если при обращении к словарю указывается ключ, которого нет в словаре,
возникает ошибка:

.. code:: python

    In [16]: london = {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco'}

    In [17]: london['ios']
    ---------------------------------------------------------------------------
    KeyError                                  Traceback (most recent call last)
    <ipython-input-17-b4fae8480b21> in <module>()
    ----> 1 london['ios']

    KeyError: 'ios'

Метод ``get`` запрашивает ключ, и если его нет, вместо ошибки
возвращает ``None``.

.. code:: python

    In [18]: london = {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco'}

    In [19]: print(london.get('ios'))
    None

Метод get() позволяет также указывать другое значение вместо ``None``:

.. code:: python

    In [20]: print(london.get('ios', 'Ooops'))
    Ooops

``setdefault``
^^^^^^^^^^^^^^^^

Метод ``setdefault`` ищет ключ, и если его нет, вместо ошибки создает
ключ со значением ``None``.

.. code:: python

    In [21]: london = {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco'}

    In [22]: ios = london.setdefault('ios')

    In [23]: print(ios)
    None

    In [24]: london
    Out[24]: {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco', 'ios': None}

Если ключ есть, setdefault возвращает значение, которое ему
соответствует:

.. code:: python

    In [25]: london.setdefault('name')
    Out[25]: 'London1'

Второй аргумент позволяет указать, какое значение должно соответствовать
ключу:

.. code:: python

    In [26]: model = london.setdefault('model', 'Cisco3580')

    In [27]: print(model)
    Cisco3580

    In [28]: london
    Out[28]:
    {'name': 'London1',
     'location': 'London Str',
     'vendor': 'Cisco',
     'ios': None,
     'model': 'Cisco3580'}


Метод setdefault заменяет такую конструкцию:

.. code:: python

    In [30]: if key in london:
        ...:     value = london[key]
        ...: else:
        ...:     london[key] = 'somevalue'
        ...:     value = london[key]
        ...:

``keys, values, items``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Методы ``keys``, ``values``, ``items``:

.. code:: python

    In [24]: london = {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco'}

    In [25]: london.keys()
    Out[25]: dict_keys(['name', 'location', 'vendor'])

    In [26]: london.values()
    Out[26]: dict_values(['London1', 'London Str', 'Cisco'])

    In [27]: london.items()
    Out[27]: dict_items([('name', 'London1'), ('location', 'London Str'), ('vendor', 'Cisco')])

Все три метода возвращают специальные объекты view, которые отображают
ключи, значения и пары ключ-значение словаря соответственно.

Очень важная особенность view заключается в том, что они меняются вместе
с изменением словаря. И фактически они лишь дают способ посмотреть на
соответствующие объекты, но не создают их копию.

На примере метода keys:

.. code:: python

    In [28]: london = {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco'}

    In [29]: keys = london.keys()

    In [30]: print(keys)
    dict_keys(['name', 'location', 'vendor'])

Сейчас переменной keys соответствует view ``dict_keys``, в котором три
ключа: name, location и vendor.

Если добавить в словарь еще одну пару ключ-значение, объект keys
тоже поменяется:

.. code:: python

    In [31]: london['ip'] = '10.1.1.1'

    In [32]: keys
    Out[32]: dict_keys(['name', 'location', 'vendor', 'ip'])

Если нужно получить обычный список ключей, который не будет меняться с
изменениями словаря, достаточно конвертировать view в список:

.. code:: python

    In [33]: list_keys = list(london.keys())

    In [34]: list_keys
    Out[34]: ['name', 'location', 'vendor', 'ip']

``del``
^^^^^^^

Удалить ключ и значение:

.. code:: python

    In [35]: london = {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco'}

    In [36]: del london['name']

    In [37]: london
    Out[37]: {'location': 'London Str', 'vendor': 'Cisco'}

``update``
^^^^^^^^^^

Метод update позволяет добавлять в словарь содержимое другого словаря:

.. code:: python

    In [38]: r1 = {'name': 'London1', 'location': 'London Str'}

    In [39]: r1.update({'vendor': 'Cisco', 'ios':'15.2'})

    In [40]: r1
    Out[40]: {'name': 'London1', 'location': 'London Str', 'vendor': 'Cisco', 'ios': '15.2'}

Аналогичным образом можно обновить значения:

.. code:: python

    In [41]: r1.update({'name': 'london-r1', 'ios':'15.4'})

    In [42]: r1
    Out[42]:
    {'name': 'london-r1',
     'location': 'London Str',
     'vendor': 'Cisco',
     'ios': '15.4'}

