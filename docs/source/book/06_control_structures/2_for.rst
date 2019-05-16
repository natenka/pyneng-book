for
---

Очень часто одно и то же действие надо выполнить для набора однотипных данных.
Например, преобразовать все строки в списке в верхний регистр. Для выполнения таких действий
в Python используется цикл ``for``.

Цикл for перебирает по одному элементы указанной последовательности и выполняет
действия, которые указаны в блоке for, для каждого элемента.

Примеры последовательностей элементов, по которым может проходиться цикл
for:

-  строка
-  список
-  словарь
-  функция :doc: `../10_useful_functions/range.rst`
-  любой `итерируемый объект ../13_iterator_generator/iterable.rst`

Цикл for проходится по строке:

.. code:: python

    In [1]: for letter in 'Test string':
       ...:     print(letter)
       ...:     
    T
    e
    s
    t

    s
    t
    r
    i
    n
    g

В цикле используется переменная с именем **letter**. Хотя имя может быть
любое, удобно, когда имя подсказывает, через какие объекты проходит
цикл.

Пример цикла for с функцией range():

.. code:: python

    In [2]: for i in range(10):
       ...:     print('interface FastEthernet0/{}'.format(i))
       ...:     
    interface FastEthernet0/0
    interface FastEthernet0/1
    interface FastEthernet0/2
    interface FastEthernet0/3
    interface FastEthernet0/4
    interface FastEthernet0/5
    interface FastEthernet0/6
    interface FastEthernet0/7
    interface FastEthernet0/8
    interface FastEthernet0/9

В этом цикле используется range(10). Range генерирует числа в диапазоне
от нуля до указанного числа (в данном примере - до 10), не включая его.

В этом примере цикл проходит по списку VLANов, поэтому переменную можно
назвать vlan:

.. code:: python

    In [3]: vlans = [10, 20, 30, 40, 100]
    In [4]: for vlan in vlans:
       ...:     print('vlan {}'.format(vlan))
       ...:     print(' name VLAN_{}'.format(vlan))
       ...:     
    vlan 10
     name VLAN_10
    vlan 20
     name VLAN_20
    vlan 30
     name VLAN_30
    vlan 40
     name VLAN_40
    vlan 100
     name VLAN_100

Когда цикл идет по словарю, то фактически он проходится по ключам:

.. code:: python

    In [5]: r1 = {
     'IOS': '15.4',
     'IP': '10.255.0.1',
     'hostname': 'london_r1',
     'location': '21 New Globe Walk',
     'model': '4451',
     'vendor': 'Cisco'}

    In [6]: for k in r1:
       ....:     print(k)
       ....:     
    vendor
    IP
    hostname
    IOS
    location
    model

Если необходимо выводить пары ключ-значение в цикле:

.. code:: python

    In [7]: for key in r1:
       ....:     print(key + ' => ' + r1[key])
       ....:     
    vendor => Cisco
    IP => 10.255.0.1
    hostname => london_r1
    IOS => 15.4
    location => 21 New Globe Walk
    model => 4451

В словаре есть специальный метод items, который позволяет проходится в
цикле сразу по паре ключ:значение:

.. code:: python

    In [8]: for key, value in r1.items():
       ....:     print(key + ' => ' + value)
       ....:     
    vendor => Cisco
    IP => 10.255.0.1
    hostname => london_r1
    IOS => 15.4
    location => 21 New Globe Walk
    model => 4451

Метод items возвращает специальный объект view, который отображает пары
ключ-значение:

.. code:: python

    In [9]: r1.items()
    Out[9]: dict_items([('IOS', '15.4'), ('IP', '10.255.0.1'), ('hostname', 'london_r1'), ('location', '21 New Globe Walk'), ('model', '4451'), ('vendor', 'Cisco')])


.. toctree::
   :maxdepth: 1

   2a_for_in_for
   2b_for_if
