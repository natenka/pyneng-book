Варианты создания списка
~~~~~~~~~~~~~~~~~~~~~~~~

Создание списка с помощью литерала:

.. code:: python

    In [1]: vlans = [10, 20, 30, 50]

    Литерал - это выражение, которое создает объект.

Создание списка с помощью функции **list()**:

.. code:: python

    In [2]: list1 = list('router')

    In [3]: print(list1)
    ['r', 'o', 'u', 't', 'e', 'r']

**Генераторы списков**:

.. code:: python

    In [4]: list2 = ['FastEthernet0/'+ str(i) for i in range(10)]

    In [5]: list2
    Out[6]: 
    ['FastEthernet0/0',
     'FastEthernet0/1',
     'FastEthernet0/2',
     'FastEthernet0/3',
     'FastEthernet0/4',
     'FastEthernet0/5',
     'FastEthernet0/6',
     'FastEthernet0/7',
     'FastEthernet0/8',
     'FastEthernet0/9']

Генераторы списков требуют понимания работы цикла for и даже после
этого, могут быть немного необычны.

После 6 раздела, можно вернуться к этой теме и почитать о них подробнее
в разделе `Примеры использования
основ <../08_python_basic_examples/x_comprehensions.md>`__.
