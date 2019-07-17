Методы __str__, __repr__
~~~~~~~~~~~~~~~~~~~~~~~~

Специальные методы __str__ и __repr__ отвечают за строковое представления
объекта. При этом используются они в разных местах.

Рассмотрим пример класса IPAddress, который отвечает за представление
IPv4 адреса:

.. code:: python

    In [1]: class IPAddress:
       ...:     def __init__(self, ip):
       ...:         self.ip = ip
       ...:

После создания экземпляров класса, у них есть строковое представление по
умолчанию, которое выглядит так (этот же вывод отображается при использовании print):

.. code:: python

    In [2]: ip1 = IPAddress('10.1.1.1')

    In [3]: ip2 = IPAddress('10.2.2.2')

    In [4]: str(ip1)
    Out[4]: '<__main__.IPAddress object at 0xb4e4e76c>'

    In [5]: str(ip2)
    Out[5]: '<__main__.IPAddress object at 0xb1bd376c>'

К сожалению, это представление не очень информативно. И было бы лучше, если бы
отображалась информация о том, какой именно адрес представляет этот экземпляр.
За отображение информации при применении функции str, отвечает специальный метод __str__ -
как аргумент метод ожидает только экземпляр и должен возвращать строку

.. code:: python

    In [6]: class IPAddress:
       ...:     def __init__(self, ip):
       ...:         self.ip = ip
       ...:
       ...:     def __str__(self):
       ...:         return f"IPAddress: {self.ip}"
       ...:

    In [7]: ip1 = IPAddress('10.1.1.1')

    In [8]: ip2 = IPAddress('10.2.2.2')

    In [9]: str(ip1)
    Out[9]: 'IPAddress: 10.1.1.1'

    In [10]: str(ip2)
    Out[10]: 'IPAddress: 10.2.2.2'

Второе строковое представление, которое используется в объектах Python, отображается
при использовании функции repr, а также при добавлении объектов в контейнеры типа списков:


.. code:: python

    In [11]: ip_addresses = [ip1, ip2]

    In [12]: ip_addresses
    Out[12]: [<__main__.IPAddress at 0xb4e40c8c>, <__main__.IPAddress at 0xb1bc46ac>]

    In [13]: repr(ip1)
    Out[13]: '<__main__.IPAddress object at 0xb4e40c8c>'

За это отображение отвечает метод __repr__, он тоже должен возвращать строку,
но при этом принято, чтобы метод возвращал строку, скопировав которую, можно 
получить экземпляр класса:

.. code:: python

    In [14]: class IPAddress:
        ...:     def __init__(self, ip):
        ...:         self.ip = ip
        ...:
        ...:     def __str__(self):
        ...:         return f"IPAddress: {self.ip}"
        ...:
        ...:     def __repr__(self):
        ...:         return f"IPAddress('{self.ip}')"
        ...:

    In [15]: ip1 = IPAddress('10.1.1.1')

    In [16]: ip2 = IPAddress('10.2.2.2')

    In [17]: ip_addresses = [ip1, ip2]

    In [18]: ip_addresses
    Out[18]: [IPAddress('10.1.1.1'), IPAddress('10.2.2.2')]

    In [19]: repr(ip1)
    Out[19]: "IPAddress('10.1.1.1')"

