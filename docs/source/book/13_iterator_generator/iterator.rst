Итераторы
---------

Итератор (iterator) - это объект, который возвращает свои элементы по
одному за раз.

С точки зрения Python - это любой объект, у которого есть метод
``__next__``. Этот метод возвращает следующий элемент, если он есть, или
возвращает исключение StopIteration, когда элементы закончились.

Кроме того, итератор запоминает, на каком объекте он остановился в
последнюю итерацию.

В Python у каждого итератора присутствует метод ``__iter__`` - то есть,
любой итератор является итерируемым объектом. Этот метод просто
возвращает сам итератор.

Пример создания итератора из списка:

.. code:: python

    In [3]: numbers = [1, 2, 3]

    In [4]: i = iter(numbers)

Теперь можно использовать функцию next(), которая вызывает метод
``__next__``, чтобы взять следующий элемент:

.. code:: python

    In [5]: next(i)
    Out[5]: 1

    In [6]: next(i)
    Out[6]: 2

    In [7]: next(i)
    Out[7]: 3

    In [8]: next(i)
    ------------------------------------------------------------
    StopIteration              Traceback (most recent call last)
    <ipython-input-8-bed2471d02c1> in <module>()
    ----> 1 next(i)

    StopIteration:

После того, как элементы закончились, возвращается исключение
StopIteration.

Для того, чтобы итератор снова начал возвращать элементы, его надо
заново создать.

Аналогичные действия выполняются, когда цикл for проходится по списку:

.. code:: python

    In [9]: for item in numbers:
       ...:     print(item)
       ...:
    1
    2
    3

Когда мы перебираем элементы списка, к списку сначала применяется
функция iter(), чтобы создать итератор, а затем вызывается его метод
``__next__`` до тех пор, пока не возникнет исключение StopIteration.

Итераторы полезны тем, что они отдают элементы по одному. Например, при
работе с файлом это полезно тем, что в памяти будет находиться не весь
файл, а только одна строка файла.

Файл как итератор
~~~~~~~~~~~~~~~~~

Один из самых распространенных примеров итератора - файл.

Файл r1.txt:

::

    !
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    !
    no ip domain lookup
    !
    ip ssh version 2
    !

Если открыть файл обычной функцией open, мы получим объект, который
представляет файл:

.. code:: python

    In [10]: f = open('r1.txt')

Этот объект является итератором, что можно проверить, вызвав метод
``__next__``:

.. code:: python

    In [11]: f.__next__()
    Out[11]: '!\n'

    In [12]: f.__next__()
    Out[12]: 'service timestamps debug datetime msec localtime show-timezone year\n'

Аналогичным образом можно перебирать строки в цикле for:

.. code:: python

    In [13]: for line in f:
        ...:     print(line.rstrip())
        ...:
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    !
    no ip domain lookup
    !
    ip ssh version 2
    !

При работе с файлами, использование файла как итератора не просто
позволяет перебирать файл построчно - в каждую итерацию загружена только
одна строка. Это очень важно при работе с большими файлами на тысячи и
сотни тысяч строк, например, с лог-файлами.

Поэтому при работе с файлами в Python чаще всего используется
конструкция вида:

.. code:: python

    In [14]: with open('r1.txt') as f:
        ...:     for line in f:
        ...:         print(line.rstrip())
        ...:
    !
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    !
    no ip domain lookup
    !
    ip ssh version 2
    !

