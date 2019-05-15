Кортеж (Tuple)
--------------


Кортеж в Python это:

* последовательность элементов, которые разделены между собой запятой и заключены в скобки
* неизменяемый упорядоченный тип данных

Грубо говоря, кортеж - это список, который нельзя изменить. То есть, в
кортеже есть только права на чтение. Это может быть защитой от случайных
изменений.

Создать пустой кортеж:

.. code:: python

    In [1]: tuple1 = tuple()

    In [2]: print(tuple1)
    ()

Кортеж из одного элемента (обратите внимание на запятую):

.. code:: python

    In [3]: tuple2 = ('password',)

Кортеж из списка:

.. code:: python

    In [4]: list_keys = ['hostname', 'location', 'vendor', 'model', 'ios', 'ip']

    In [5]: tuple_keys = tuple(list_keys)

    In [6]: tuple_keys
    Out[6]: ('hostname', 'location', 'vendor', 'model', 'ios', 'ip')

К объектам в кортеже можно обращаться, как и к объектам списка, по
порядковому номеру:

.. code:: python

    In [7]: tuple_keys[0]
    Out[7]: 'hostname'

Но так как кортеж неизменяем, присвоить новое значение нельзя:

.. code:: python

    In [8]: tuple_keys[1] = 'test'
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-9-1c7162cdefa3> in <module>()
    ----> 1 tuple_keys[1] = 'test'

    TypeError: 'tuple' object does not support item assignment

Функция sorted сортирует элементы кортежа по возрастанию и возвращает
новый список с отсортированными элементами:

.. code:: python


    In [2]: tuple_keys = ('hostname', 'location', 'vendor', 'model', 'ios', 'ip')

    In [3]: sorted(tuple_keys)
    Out[3]: ['hostname', 'ios', 'ip', 'location', 'model', 'vendor']

