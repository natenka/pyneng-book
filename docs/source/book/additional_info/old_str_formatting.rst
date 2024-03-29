.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

Форматирование строк с оператором ``%``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Пример использования оператора %:

.. code:: python

    In [2]: "interface FastEthernet0/%s" % '1'
    Out[2]: 'interface FastEthernet0/1'

В старом синтаксисе форматирования строк используются такие обозначения:

* ``%s`` - строка или любой другой объект в котором есть строковое представление
* ``%d`` - integer
* ``%f`` - float

Вывести данные столбцами одинаковой ширины по 15 символов с
выравниванием по правой стороне:

.. code:: python

    In [3]: vlan, mac, intf = ['100', 'aabb.cc80.7000', 'Gi0/1']

    In [4]: print("%15s %15s %15s" % (vlan, mac, intf))
                100  aabb.cc80.7000           Gi0/1

Выравнивание по левой стороне:

.. code:: python

    In [6]: print("%-15s %-15s %-15s" % (vlan, mac, intf))
    100             aabb.cc80.7000  Gi0/1

С помощью форматирования строк можно также влиять на отображение чисел.

Например, можно указать, сколько цифр после запятой выводить:

.. code:: python

    In [8]: print("%.3f" % (10.0 / 3))
    3.333

.. note::
    У форматирования строк есть ещё много возможностей. Хорошие примеры
    и объяснения двух вариантов форматирования строк можно найти
    `тут <https://pyformat.info/>`__.
