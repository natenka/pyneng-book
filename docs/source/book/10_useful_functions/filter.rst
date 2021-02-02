Функция filter
--------------

Функция ``filter`` применяет функцию ко всем элементам последовательности
и возвращает итератор с теми объектами, для которых функция вернула
True.

Например, вернуть только те строки, в которых находятся числа:

.. code:: python

    In [1]: list_of_strings = ['one', 'two', 'list', '', 'dict', '100', '1', '50']

    In [2]: filter(str.isdigit, list_of_strings)
    Out[2]: <filter at 0xb45eb1cc>

    In [3]: list(filter(str.isdigit, list_of_strings))
    Out[3]: ['100', '1', '50']

Из списка чисел оставить только нечетные:

.. code:: python

    In [3]: list(filter(lambda x: x % 2 == 1, [10, 111, 102, 213, 314, 515]))
    Out[3]: [111, 213, 515]

Аналогично, только четные:

.. code:: python

    In [4]: list(filter(lambda x: x % 2 == 0, [10, 111, 102, 213, 314, 515]))
    Out[4]: [10, 102, 314]

Из списка слов оставить только те, у которых количество букв больше
двух:

.. code:: python

    In [5]: list_of_words = ['one', 'two', 'list', '', 'dict']

    In [6]: list(filter(lambda x: len(x) > 2, list_of_words))
    Out[6]: ['one', 'two', 'list', 'dict']

List comprehension вместо filter
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Как правило, вместо filter можно использовать list comprehension.

Примеры, аналогичные приведенным выше, в варианте с list comprehension.

Вернуть только те строки, в которых находятся числа:

.. code:: python

    In [7]: list_of_strings = ['one', 'two', 'list', '', 'dict', '100', '1', '50']

    In [8]: [s for s in list_of_strings if s.isdigit()]
    Out[8]: ['100', '1', '50']

Нечетные/четные числа:

.. code:: python

    In [9]: nums = [10, 111, 102, 213, 314, 515]

    In [10]: [n for n in nums if n % 2 == 1]
    Out[10]: [111, 213, 515]

    In [11]: [n for n in nums if n % 2 == 0]
    Out[11]: [10, 102, 314]

Из списка слов оставить только те, у которых количество букв больше
двух:

.. code:: python

    In [12]: list_of_words = ['one', 'two', 'list', '', 'dict']

    In [13]: [word for word in list_of_words if len(word) > 2]
    Out[13]: ['one', 'two', 'list', 'dict']

