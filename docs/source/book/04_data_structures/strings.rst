Строки (Strings)
================

Строка в Python это:

* последовательность символов, заключенная в кавычки
* неизменяемый упорядоченный тип данных

Примеры строк:

.. code:: python

    In [9]: 'Hello'
    Out[9]: 'Hello'
    In [10]: "Hello"
    Out[10]: 'Hello'

    In [11]: tunnel = """
       ....: interface Tunnel0
       ....:  ip address 10.10.10.1 255.255.255.0
       ....:  ip mtu 1416
       ....:  ip ospf hello-interval 5
       ....:  tunnel source FastEthernet1/0
       ....:  tunnel protection ipsec profile DMVPN
       ....: """

    In [12]: tunnel
    Out[12]: '\ninterface Tunnel0\n ip address 10.10.10.1 255.255.255.0\n ip mtu 1416\n ip ospf hello-interval 5\n tunnel source FastEthernet1/0\n tunnel protection ipsec profile DMVPN\n'

    In [13]: print(tunnel)

    interface Tunnel0
     ip address 10.10.10.1 255.255.255.0
     ip mtu 1416
     ip ospf hello-interval 5
     tunnel source FastEthernet1/0
     tunnel protection ipsec profile DMVPN

Строки можно суммировать. Тогда они объединяются в одну строку:

.. code:: python

    In [14]: intf = 'interface'

    In [15]: tun = 'Tunnel0'

    In [16]: intf + tun
    Out[16]: 'interfaceTunnel0'

    In [17]: intf + ' ' + tun
    Out[17]: 'interface Tunnel0'

Строку можно умножать на число. В этом случае, строка повторяется
указанное количество раз:

.. code:: python

    In [18]: intf * 5
    Out[18]: 'interfaceinterfaceinterfaceinterfaceinterface'

    In [19]: '#' * 40
    Out[19]: '########################################'

То, что строки являются упорядоченным типом данных, позволяет обращаться
к символам в строке по номеру, начиная с нуля:

.. code:: python

    In [20]: string1 = 'interface FastEthernet1/0'

    In [21]: string1[0]
    Out[21]: 'i'

Нумерация всех символов в строке идет с нуля. Но, если нужно обратиться
к какому-то по счету символу, начиная с конца, то можно указывать
отрицательные значения (на этот раз с единицы).

.. code:: python

    In [22]: string1[1]
    Out[22]: 'n'

    In [23]: string1[-1]
    Out[23]: '0'

Кроме обращения к конкретному символу, можно делать срезы строк, указав
диапазон номеров (срез выполняется по второе число, не включая его):

.. code:: python

    In [24]: string1[0:9]
    Out[24]: 'interface'

    In [25]: string1[10:22]
    Out[25]: 'FastEthernet'

Если не указывается второе число, то срез будет до конца строки:

.. code:: python

    In [26]:  string1[10:]
    Out[26]: 'FastEthernet1/0'

Срезать три последних символа строки:

.. code:: python

    In [27]: string1[-3:]
    Out[27]: '1/0'

Также в срезе можно указывать шаг. Так можно получить нечетные числа:

.. code:: python

    In [28]: a = '0123456789'

    In [29]: a[1::2]
    Out[29]: '13579'

А таким образом можно получить все четные числа строки a:

.. code:: python

    In [31]: a[::2]
    Out[31]: '02468'

Срезы также можно использовать для получения строки в обратном порядке:

.. code:: python

    In [28]: a = '0123456789'

    In [29]: a[::]
    Out[29]: '0123456789'

    In [30]: a[::-1]
    Out[30]: '9876543210'

.. note::
    Записи ``a[::]`` и ``a[:]`` дают одинаковый результат, но двойное
    двоеточие позволяет указывать, что надо брать не каждый элемент, а,
    например, каждый второй.

Функция ``len`` позволяет получить количество символов в строке:

.. code:: python

    In [1]: line = 'interface Gi0/1'

    In [2]: len(line)
    Out[2]: 15

.. note::
    Функция и метод отличаются тем, что метод привязан к объекту конкретного типа, а функция,
    как правило, более универсальная и может применяться к объектам разного типа.
    Например, функция ``len`` может применяться к строкам, спискам, словарям и так далее,
    а метод ``startswith`` относится только к строкам.


.. toctree::
   :maxdepth: 1
   :hidden:

   string_methods
   string_format
   string_literal_concatenation
