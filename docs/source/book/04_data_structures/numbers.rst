.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

Числа
=====

С числами можно выполнять различные математические операции.

.. code:: python

    In [1]: 1 + 2
    Out[1]: 3

    In [2]: 1.0 + 2
    Out[2]: 3.0

    In [3]: 10 - 4
    Out[3]: 6

    In [4]: 2**3
    Out[4]: 8

Деление int и float:

.. code:: python

    In [5]: 10/3
    Out[5]: 3.3333333333333335

    In [6]: 10/3.0
    Out[6]: 3.3333333333333335

С помощью функции round можно округлять числа до нужного количества
знаков:

.. code:: python

    In [9]: round(10/3.0, 2)
    Out[9]: 3.33

    In [10]: round(10/3.0, 4)
    Out[10]: 3.3333

Остаток от деления:

.. code:: python

    In [11]: 10 % 3
    Out[11]: 1

Операторы сравнения

.. code:: python

    In [12]: 10 > 3.0
    Out[12]: True

    In [13]: 10 < 3
    Out[13]: False

    In [14]: 10 == 3
    Out[14]: False

    In [15]: 10 == 10
    Out[15]: True

    In [16]: 10 <= 10
    Out[16]: True

    In [17]: 10.0 == 10
    Out[17]: True

Функция int() позволяет выполнять конвертацию в тип int. Во втором
аргументе можно указывать систему счисления:

.. code:: python

    In [18]: a = '11'

    In [19]: int(a)
    Out[19]: 11

Если указать, что строку a надо воспринимать как двоичное число, то
результат будет таким:

.. code:: python

    In [20]: int(a, 2)
    Out[20]: 3

Конвертация в int типа float:

.. code:: python

    In [21]: int(3.333)
    Out[21]: 3

    In [22]: int(3.9)
    Out[22]: 3

Функция bin позволяет получить двоичное представление числа (обратите
внимание, что результат - строка):

.. code:: python

    In [23]: bin(8)
    Out[23]: '0b1000'

    In [24]: bin(255)
    Out[24]: '0b11111111'

Аналогично, функция hex() позволяет получить шестнадцатеричное значение:

.. code:: python

    In [25]: hex(10)
    Out[25]: '0xa'

И, конечно же, можно делать несколько преобразований одновременно:

.. code:: python

    In [26]: int('ff', 16)
    Out[26]: 255

    In [27]: bin(int('ff', 16))
    Out[27]: '0b11111111'

Для более сложных математических функций в Python есть модуль **math**:

.. code:: python

    In [28]: import  math

    In [29]: math.sqrt(9)
    Out[29]: 3.0

    In [30]: math.sqrt(10)
    Out[30]: 3.1622776601683795

    In [31]: math.factorial(3)
    Out[31]: 6

    In [32]: math.pi
    Out[32]: 3.141592653589793
