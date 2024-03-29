.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

Основы сортировки данных
========================

При сортировке данных типа списка списков или списка кортежей,
``sorted`` сортирует по первому элементу вложенных списков (кортежей),
а если первый элемент одинаковый, по второму:

.. code:: python

    In [1]: data = [[1, 100, 1000], [2, 2, 2], [1, 2, 3], [4, 100, 3]]

    In [2]: sorted(data)
    Out[2]: [[1, 2, 3], [1, 100, 1000], [2, 2, 2], [4, 100, 3]]


Если сортировка делается для списка чисел, которые записаны как строки,
сортировка будет лексикографической, не натуральной и порядок будет таким:

.. code:: python

    In [7]: vlans = ['1', '30', '11', '3', '10', '20', '30', '100']

    In [8]: sorted(vlans)
    Out[8]: ['1', '10', '100', '11', '20', '3', '30', '30']

Чтобы сортировка была "правильной" надо преобразовать вланы в числа.

Эта же проблема проявляется, например, с IP-адресами:

.. code:: python

    In [2]: ip_list = ["10.1.1.1", "10.1.10.1", "10.1.2.1", "10.1.11.1"]

    In [3]: sorted(ip_list)
    Out[3]: ['10.1.1.1', '10.1.10.1', '10.1.11.1', '10.1.2.1']

Как решить проблему с сортировкой IP-адресов рассматривается в разделе `10. Полезные функции <https://pyneng.readthedocs.io/ru/latest/book/10_useful_functions/index.html>`__.
