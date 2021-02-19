Функция map
-----------

Функция map применяет функцию к каждому элементу последовательности и
возвращает итератор с результатами.

Например, с помощью map можно выполнять преобразования элементов.
Перевести все строки в верхний регистр:

.. code:: python

    In [1]: list_of_words = ['one', 'two', 'list', '', 'dict']

    In [2]: map(str.upper, list_of_words)
    Out[2]: <map at 0xb45eb7ec>

    In [3]: list(map(str.upper, list_of_words))
    Out[3]: ['ONE', 'TWO', 'LIST', '', 'DICT']

.. note::

    ``str.upper("aaa")`` делает то же самое что ``"aaa".upper()``.

Конвертация в числа:

.. code:: python

    In [3]: list_of_str = ['1', '2', '5', '10']

    In [4]: list(map(int, list_of_str))
    Out[4]: [1, 2, 5, 10]

Вместе с map удобно использовать лямбда-выражения:

.. code:: python

    In [5]: vlans = [100, 110, 150, 200, 201, 202]

    In [6]: list(map(lambda x: 'vlan {}'.format(x), vlans))
    Out[6]: ['vlan 100', 'vlan 110', 'vlan 150', 'vlan 200', 'vlan 201', 'vlan 202']

Если функция, которую использует map(), ожидает два аргумента, то
передаются два списка:

.. code:: python

    In [7]: nums = [1, 2, 3, 4, 5]

    In [8]: nums2 = [100, 200, 300, 400, 500]

    In [9]: list(map(lambda x, y: x*y, nums, nums2))
    Out[9]: [100, 400, 900, 1600, 2500]

List comprehension вместо map
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Как правило, вместо map можно использовать list comprehension. Чаще
всего, вариант с list comprehension более понятный, а в некоторых
случаях даже быстрее.

    `Ответ Alex Martelli со сравнением map и list
    comprehension <https://stackoverflow.com/a/1247490>`__

Но map может быть эффективней в том случае, когда надо сгенерировать
большое количество элементов, так как map - итератор, а list
comprehension генерирует список.

Примеры, аналогичные приведенным выше, в варианте с list comprehension.

Перевести все строки в верхний регистр:

.. code:: python

    In [48]: list_of_words = ['one', 'two', 'list', '', 'dict']

    In [49]: [word.upper() for word in list_of_words]
    Out[49]: ['ONE', 'TWO', 'LIST', '', 'DICT']

Конвертация в числа:

.. code:: python

    In [50]: list_of_str = ['1', '2', '5', '10']

    In [51]: [int(i) for i in list_of_str]
    Out[51]: [1, 2, 5, 10]

Форматирование строк:

.. code:: python

    In [52]:  vlans = [100, 110, 150, 200, 201, 202]

    In [53]: [f'vlan {x}' for x in vlans]
    Out[53]: ['vlan 100', 'vlan 110', 'vlan 150', 'vlan 200', 'vlan 201', 'vlan 202']

Для получения пар элементов используется zip:

.. code:: python

    In [54]: nums = [1, 2, 3, 4, 5]

    In [55]: nums2 = [100, 200, 300, 400, 500]

    In [56]: [x * y for x, y in zip(nums, nums2)]
    Out[56]: [100, 400, 900, 1600, 2500]

