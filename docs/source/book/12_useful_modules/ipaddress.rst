Модуль ipaddress
----------------

Модуль ipaddress упрощает работу с IP-адресами.

.. note::
    С версии Python 3.3 модуль ipaddress входит в стандартную библиотеку
    Python.

``ipaddress.ip_address``
~~~~~~~~~~~~~~~~~~~~~~~~~~

Функция ``ipaddress.ip_address`` позволяет создавать объект
IPv4Address или IPv6Address соответственно:

.. code:: python

    In [1]: import ipaddress

    In [2]: ipv4 = ipaddress.ip_address('10.0.1.1')

    In [3]: ipv4
    Out[3]: IPv4Address('10.0.1.1')

    In [4]: print(ipv4)
    10.0.1.1

У объекта есть несколько методов и атрибутов:

.. code:: python

    In [5]: ipv4.
    ipv4.compressed       ipv4.is_loopback      ipv4.is_unspecified   ipv4.version
    ipv4.exploded         ipv4.is_multicast     ipv4.max_prefixlen
    ipv4.is_global        ipv4.is_private       ipv4.packed
    ipv4.is_link_local    ipv4.is_reserved      ipv4.reverse_pointer

С помощью атрибутов ``is_`` можно проверить, к какому диапазону принадлежит
адрес:

.. code:: python

    In [6]: ipv4.is_loopback
    Out[6]: False

    In [7]: ipv4.is_multicast
    Out[7]: False

    In [8]: ipv4.is_reserved
    Out[8]: False

    In [9]: ipv4.is_private
    Out[9]: True

С полученными объектами можно выполнять различные операции:

.. code:: python

    In [10]: ip1 = ipaddress.ip_address('10.0.1.1')

    In [11]: ip2 = ipaddress.ip_address('10.0.2.1')

    In [12]: ip1 > ip2
    Out[12]: False

    In [13]: ip2 > ip1
    Out[13]: True

    In [14]: ip1 == ip2
    Out[14]: False

    In [15]: ip1 != ip2
    Out[15]: True

    In [16]: str(ip1)
    Out[16]: '10.0.1.1'

    In [17]: int(ip1)
    Out[17]: 167772417

    In [18]: ip1 + 5
    Out[18]: IPv4Address('10.0.1.6')

    In [19]: ip1 - 5
    Out[19]: IPv4Address('10.0.0.252')

``ipaddress.ip_network``
~~~~~~~~~~~~~~~~~~~~~~~~~~

Функция ``ipaddress.ip_network`` позволяет создать объект, который
описывает сеть (IPv4 или IPv6):

.. code:: python

    In [20]: subnet1 = ipaddress.ip_network('80.0.1.0/28')

Как и у адреса, у сети есть различные атрибуты и методы:

.. code:: python

    In [21]: subnet1.broadcast_address
    Out[21]: IPv4Address('80.0.1.15')

    In [22]: subnet1.with_netmask
    Out[22]: '80.0.1.0/255.255.255.240'

    In [23]: subnet1.with_hostmask
    Out[23]: '80.0.1.0/0.0.0.15'

    In [24]: subnet1.prefixlen
    Out[24]: 28

    In [25]: subnet1.num_addresses
    Out[25]: 16

Метод ``hosts`` возвращает генератор, поэтому, чтобы посмотреть все хосты,
надо применить функцию ``list``:

.. code:: python

    In [26]: list(subnet1.hosts())
    Out[26]:
    [IPv4Address('80.0.1.1'),
     IPv4Address('80.0.1.2'),
     IPv4Address('80.0.1.3'),
     IPv4Address('80.0.1.4'),
     IPv4Address('80.0.1.5'),
     IPv4Address('80.0.1.6'),
     IPv4Address('80.0.1.7'),
     IPv4Address('80.0.1.8'),
     IPv4Address('80.0.1.9'),
     IPv4Address('80.0.1.10'),
     IPv4Address('80.0.1.11'),
     IPv4Address('80.0.1.12'),
     IPv4Address('80.0.1.13'),
     IPv4Address('80.0.1.14')]

Метод subnets позволяет разбивать на подсети. По умолчанию он разбивает
сеть на две подсети:

.. code:: python

    In [27]: list(subnet1.subnets())
    Out[27]: [IPv4Network('80.0.1.0/29'), IPv4Network('80.0.1.8/29')]

Параметр prefixlen_diff позволяет указать количество бит
для подсетей:

.. code:: python

    In [28]: list(subnet1.subnets(prefixlen_diff=2))
    Out[28]:
    [IPv4Network('80.0.1.0/30'),
     IPv4Network('80.0.1.4/30'),
     IPv4Network('80.0.1.8/30'),
     IPv4Network('80.0.1.12/30')]

С помощью параметра new_prefix можно указать, какая маска должна
быть у подсетей:

.. code:: python

    In [29]: list(subnet1.subnets(new_prefix=30))
    Out[29]:
    [IPv4Network('80.0.1.0/30'),
     IPv4Network('80.0.1.4/30'),
     IPv4Network('80.0.1.8/30'),
     IPv4Network('80.0.1.12/30')]

    In [30]: list(subnet1.subnets(new_prefix=29))
    Out[30]: [IPv4Network('80.0.1.0/29'), IPv4Network('80.0.1.8/29')]

По IP-адресам в сети можно проходиться в цикле:

.. code:: python

    In [31]: for ip in subnet1:
       ....:     print(ip)
       ....:
    80.0.1.0
    80.0.1.1
    80.0.1.2
    80.0.1.3
    80.0.1.4
    80.0.1.5
    80.0.1.6
    80.0.1.7
    80.0.1.8
    80.0.1.9
    80.0.1.10
    80.0.1.11
    80.0.1.12
    80.0.1.13
    80.0.1.14
    80.0.1.15

Или обращаться к конкретному адресу:

.. code:: python

    In [32]: subnet1[0]
    Out[32]: IPv4Address('80.0.1.0')

    In [33]: subnet1[5]
    Out[33]: IPv4Address('80.0.1.5')

Таким образом можно проверять, находится ли IP-адрес в сети:

.. code:: python

    In [34]: ip1 = ipaddress.ip_address('80.0.1.3')

    In [35]: ip1 in subnet1
    Out[35]: True

``ipaddress.ip_interface``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Функция ``ipaddress.ip_interface`` позволяет создавать объект
IPv4Interface или IPv6Interface соответственно:

.. code:: python

    In [36]: int1 = ipaddress.ip_interface('10.0.1.1/24')

Используя методы объекта IPv4Interface, можно получать адрес, маску или
сеть интерфейса:

.. code:: python

    In [37]: int1.ip
    Out[37]: IPv4Address('10.0.1.1')

    In [38]: int1.network
    Out[38]: IPv4Network('10.0.1.0/24')

    In [39]: int1.netmask
    Out[39]: IPv4Address('255.255.255.0')

Пример использования модуля
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Так как в модуль встроены проверки корректности адресов, можно ими
пользоваться, например, чтобы проверить, является ли адрес адресом сети
или хоста:

.. code:: python

    In [40]: IP1 = '10.0.1.1/24'

    In [41]: IP2 = '10.0.1.0/24'

    In [42]: def check_if_ip_is_network(ip_address):
       ....:     try:
       ....:         ipaddress.ip_network(ip_address)
       ....:         return True
       ....:     except ValueError:
       ....:         return False
       ....:

    In [43]: check_if_ip_is_network(IP1)
    Out[43]: False

    In [44]: check_if_ip_is_network(IP2)
    Out[44]: True

