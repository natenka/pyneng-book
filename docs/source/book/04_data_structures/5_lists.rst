Список (List)
=============

Список - это изменяемый упорядоченный тип данных.

Список в Python - это последовательность элементов, разделенных между
собой запятой и заключенных в квадратные скобки.

Примеры списков:

.. code:: python

    In [1]: list1 = [10,20,30,77]
    In [2]: list2 = ['one', 'dog', 'seven']
    In [3]: list3 = [1, 20, 4.0, 'word']

Так как список - это упорядоченный тип данных, то, как и в строках, в
списках можно обращаться к элементу по номеру, делать срезы:

.. code:: python

    In [4]: list3 = [1, 20, 4.0, 'word']

    In [5]: list3[1]
    Out[5]: 20

    In [6]: list3[1::]
    Out[6]: [20, 4.0, 'word']

    In [7]: list3[-1]
    Out[7]: 'word'

    In [8]: list3[::-1]
    Out[8]: ['word', 4.0, 20, 1]

Перевернуть список наоборот можно и с помощью метода reverse():

.. code:: python

    In [10]: vlans = ['10', '15', '20', '30', '100-200']

    In [11]: vlans.reverse()

    In [12]: vlans
    Out[12]: ['100-200', '30', '20', '15', '10']

Так как списки изменяемые, элементы списка можно менять:

.. code:: python

    In [13]: list3
    Out[13]: [1, 20, 4.0, 'word']

    In [14]: list3[0] = 'test'

    In [15]: list3
    Out[15]: ['test', 20, 4.0, 'word']

Можно создавать и список списков. И, как и в обычном списке, можно
обращаться к элементам во вложенных списках:

.. code:: python

    In [16]: interfaces = [['FastEthernet0/0', '15.0.15.1', 'YES', 'manual', 'up', 'up'],
       ....: ['FastEthernet0/1', '10.0.1.1', 'YES', 'manual', 'up', 'up'],
       ....: ['FastEthernet0/2', '10.0.2.1', 'YES', 'manual', 'up', 'down']]

    In [17]: interfaces[0][0]
    Out[17]: 'FastEthernet0/0'

    In [18]: interfaces[2][0]
    Out[18]: 'FastEthernet0/2'

    In [19]: interfaces[2][1]
    Out[19]: '10.0.2.1'

.. toctree::
   :maxdepth: 1

   5a_list_methods
   5b_create_list
