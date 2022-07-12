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
-  :ref:`range`
-  любой :ref:`iterable`

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

Выражение ``for word in words: upper_words.append(word.upper())``
означает "для каждого слова в списке words выполнить действия в блоке for".
При этом word это имя переменной, которое каждую итерацию цикла ссылается на разные значения.

.. note::
    `Проект pythontutor <http://www.pythontutor.com/>`__ может очень помочь в понимании циклов.
    Там есть специальная визуализация кода, которая позволяет увидеть, что происходит
    на каждом этапе выполнения кода, что особенно полезно на первых порах с циклами.
    На `сайте pythontutor <http://www.pythontutor.com/visualize.html#mode=edit>`__ можно загружать свой код, но для примера, по этой ссылке можно посмотреть
    `пример выше <http://www.pythontutor.com/visualize.html#code=words%20%3D%20%5B'list',%20'dict',%20'tuple'%5D%0Aupper_words%20%3D%20%5B%5D%0A%0Afor%20word%20in%20words%3A%0A%20%20%20%20upper_words.append%28word.upper%28%29%29%0A&cumulative=false&curInstr=0&heapPrimitives=nevernest&mode=display&origin=opt-frontend.js&py=3&rawInputLstJSON=%5B%5D&textReferences=false>`__.


Цикл for может работать с любой последовательностью элементов.
Например, выше использовался список и цикл перебирал элементы списка.
Аналогичным образом for работает с кортежами.

При работе со строками, цикл for перебирает символы строки, например:

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

.. note::
    В цикле используется переменная с именем **letter**. Хотя имя может быть
    любое, удобно, когда имя подсказывает, через какие объекты проходит
    цикл.


Иногда в цикле необходимо использовать последовательность чисел. В этом случае, лучше всего использовать функцию
:ref:`range`

Пример цикла for с функцией range():

.. code:: python

    In [2]: for i in range(10):
       ...:     print(f'interface FastEthernet0/{i}')
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

В этом цикле используется ``range(10)``. Функция range генерирует числа в диапазоне
от нуля до указанного числа (в данном примере - до 10), не включая его.

В этом примере цикл проходит по списку VLANов, поэтому переменную можно
назвать vlan:

.. code:: python

    In [3]: vlans = [10, 20, 30, 40, 100]
    In [4]: for vlan in vlans:
       ...:     print(f'vlan {vlan}')
       ...:     print(f' name VLAN_{vlan}')
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

    In [34]: r1 = {
        ...:      'ios': '15.4',
        ...:      'ip': '10.255.0.1',
        ...:      'hostname': 'london_r1',
        ...:      'location': '21 New Globe Walk',
        ...:      'model': '4451',
        ...:      'vendor': 'Cisco'}
        ...:

    In [35]: for k in r1:
        ...:     print(k)
        ...:
    ios
    ip
    hostname
    location
    model
    vendor


Если необходимо выводить пары ключ-значение в цикле, можно делать так:

.. code:: python

    In [36]: for key in r1:
        ...:     print(key + ' => ' + r1[key])
        ...:
    ios => 15.4
    ip => 10.255.0.1
    hostname => london_r1
    location => 21 New Globe Walk
    model => 4451
    vendor => Cisco

Или воспользоваться методом items, который позволяет проходиться в
цикле сразу по паре ключ-значение:

.. code:: python

    In [37]: for key, value in r1.items():
        ...:     print(key + ' => ' + value)
        ...:
    ios => 15.4
    ip => 10.255.0.1
    hostname => london_r1
    location => 21 New Globe Walk
    model => 4451
    vendor => Cisco


Метод items возвращает специальный объект view, который отображает пары
ключ-значение:

.. code:: python

    In [38]: r1.items()
    Out[38]: dict_items([('ios', '15.4'), ('ip', '10.255.0.1'), ('hostname', 'london_r1'), ('location', '21 New Globe Walk'), ('model', '4451'), ('vendor', 'Cisco')])


.. toctree::
   :maxdepth: 1
   :hidden:

   for_in_for
   for_if
