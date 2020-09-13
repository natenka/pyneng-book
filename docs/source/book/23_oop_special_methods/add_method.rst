Поддержка арифметических операторов
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

За поддержку арифметических операций также отвечают специальные методы,
например, за операцию сложения отвечает метод __add__:

.. code:: python

    __add__(self, other)

Добавим к классу IPAddress поддержку суммирования с числами, но чтобы
не усложнять реализацию метода, воспользуемся возможностями модуля ipaddress

.. code:: python

    In [1]: import ipaddress

    In [2]: ipaddress1 = ipaddress.ip_address('10.1.1.1')

    In [3]: int(ipaddress1)
    Out[3]: 167837953

    In [4]: ipaddress.ip_address(167837953)
    Out[4]: IPv4Address('10.1.1.1')

Класс IPAddress с методом __add__:

.. code:: python

    In [5]: class IPAddress:
       ...:     def __init__(self, ip):
       ...:         self.ip = ip
       ...:
       ...:     def __str__(self):
       ...:         return f"IPAddress: {self.ip}"
       ...:
       ...:     def __repr__(self):
       ...:         return f"IPAddress('{self.ip}')"
       ...:
       ...:     def __add__(self, other):
       ...:         ip_int = int(ipaddress.ip_address(self.ip))
       ...:         sum_ip_str = str(ipaddress.ip_address(ip_int + other))
       ...:         return IPAddress(sum_ip_str)
       ...:

Переменная ip_int ссылается на значение исходного адреса в десятичном формате.
а sum_ip_str это строка с IP-адресом полученным в результате сложения двух чисел.
Как правило, желательно чтобы операция суммирования возвращала экземпляр того же
класса, поэтому в последней строке метода создается экземпляр класса IPAddress
и ему как аргумент передается строка с итоговым адресом.

Теперь экземпляры класса IPAddress должны поддерживать операцию сложения с числом.
В результате мы получаем новый экземпляр класса IPAddress.

.. code:: python

    In [6]: ip1 = IPAddress('10.1.1.1')

    In [7]: ip1 + 5
    Out[7]: IPAddress('10.1.1.6')

Так как внутри метода используется модуль ipaddress, а он поддерживает создание
IP-адреса только из десятичного числа, надо ограничить метод на работу только с 
данными типа int. Если же второй элемент был объектом другого типа, надо
сгенерировать исключение. Исключение и сообщение об ошибке возьмем из аналогичной ошибки
функции ipaddress.ip_address:

.. code:: python

    In [8]: a1 = ipaddress.ip_address('10.1.1.1')

    In [9]: a1 + 4
    Out[9]: IPv4Address('10.1.1.5')

    In [10]: a1 + 4.0
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-10-a0a045adedc5> in <module>
    ----> 1 a1 + 4.0

    TypeError: unsupported operand type(s) for +: 'IPv4Address' and 'float'

Теперь класс IPAddress выглядит так:

.. code:: python

    In [11]: class IPAddress:
        ...:     def __init__(self, ip):
        ...:         self.ip = ip
        ...:
        ...:     def __str__(self):
        ...:         return f"IPAddress: {self.ip}"
        ...:
        ...:     def __repr__(self):
        ...:         return f"IPAddress('{self.ip}')"
        ...:
        ...:     def __add__(self, other):
        ...:         if not isinstance(other, int):
        ...:             raise TypeError(f"unsupported operand type(s) for +:"
        ...:                             f" 'IPAddress' and '{type(other).__name__}'")
        ...:
        ...:         ip_int = int(ipaddress.ip_address(self.ip))
        ...:         sum_ip_str = str(ipaddress.ip_address(ip_int + other))
        ...:         return IPAddress(sum_ip_str)
        ...:

Если второй операнд не является экзепляром класса int, генерируется исключение TypeError.
В исключении выводится информация, что суммирование не поддерживается между экземплярами
класса IPAddress и экземпляром класса операнда. Имя класса получено из самого класса,
после обращения к type: ``type(other).__name__``.

Проверка суммирования с десятичным числом и генерации ошибки:

.. code:: python

    In [12]: ip1 = IPAddress('10.1.1.1')

    In [13]: ip1 + 5
    Out[13]: IPAddress('10.1.1.6')

    In [14]: ip1 + 5.0
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-14-5e619f8dc37a> in <module>
    ----> 1 ip1 + 5.0

    <ipython-input-11-77b43bc64757> in __add__(self, other)
         11     def __add__(self, other):
         12         if not isinstance(other, int):
    ---> 13             raise TypeError(f"unsupported operand type(s) for +:"
         14                             f" 'IPAddress' and '{type(other).__name__}'")
         15

    TypeError: unsupported operand type(s) for +: 'IPAddress' and 'float'

    In [15]: ip1 + '1'
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-15-c5ce818f55d8> in <module>
    ----> 1 ip1 + '1'

    <ipython-input-11-77b43bc64757> in __add__(self, other)
         11     def __add__(self, other):
         12         if not isinstance(other, int):
    ---> 13             raise TypeError(f"unsupported operand type(s) for +:"
         14                             f" 'IPAddress' and '{type(other).__name__}'")
         15

    TypeError: unsupported operand type(s) for +: 'IPAddress' and 'str'

.. seealso:: Руководство по специальным методам (англ) `Numeric magic methods <https://rszalski.github.io/magicmethods/#numeric>`__
