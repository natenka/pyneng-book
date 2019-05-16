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
-  функция :doc:`../10_useful_functions/range.rst`
-  любой :doc:`итерируемый объект ../13_iterator_generator/iterable.rst`

Пример преобразования строк в списке в верхний регистр без цикла for:

.. code:: python

    In [1]: words = ['list', 'dict', 'tuple']

    In [2]: upper_words = []

    In [3]: words[0]
    Out[3]: 'list'

    In [4]: words[0].upper() # преобразование слова в верхний регистр
    Out[4]: 'LIST'

    In [5]: upper_words.append(words[0].upper()) # преобразование и добавление в новый список

    In [6]: upper_words
    Out[6]: ['LIST']

    In [7]: upper_words.append(words[1].upper())

    In [8]: upper_words.append(words[2].upper())

    In [9]: upper_words
    Out[9]: ['LIST', 'DICT', 'TUPLE']


У данного решения есть несколько нюансов:

* одно и то же действие надо повторять несколько раз
* код привязан к определенному количеству элементов в списке words

Те же действия с циклом for:

.. code:: python

    In [10]: words = ['list', 'dict', 'tuple']

    In [11]: upper_words = []

    In [12]: for word in words:
        ...:     upper_words.append(word.upper())
        ...:

    In [13]: upper_words
    Out[13]: ['LIST', 'DICT', 'TUPLE']

<iframe width="800" height="500" frameborder="0" src="http://pythontutor.com/iframe-embed.html#code=words%20%3D%20%5B'list',%20'dict',%20'tuple'%5D%0Aupper_words%20%3D%20%5B%5D%0A%0Afor%20word%20in%20words%3A%0A%20%20%20%20upper_words.append%28word.upper%28%29%29%0A&codeDivHeight=400&codeDivWidth=350&cumulative=false&curInstr=9&heapPrimitives=nevernest&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false"> </iframe>


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
