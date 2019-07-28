Протокол последовательности
~~~~~~~~~~~~~~~~~~~~~~~~~~~

В самом базовом варианте, протокол последовательности (sequence) включает
два метода: __len__ и __getitem__. В более полном варианте также методы:
__contains__, __iter__, __reversed__, index и count. Если последовательность изменяема,
добавляются еще несколько методов.

Добавим методы __len__ и __getitem__ к классу Network:

.. code:: python

    In [1]: class Network:
       ...:     def __init__(self, network):
       ...:         self.network = network
       ...:         subnet = ipaddress.ip_network(self.network)
       ...:         self.addresses = [str(ip) for ip in subnet.hosts()]
       ...:
       ...:     def __iter__(self):
       ...:         return iter(self.addresses)
       ...:
       ...:     def __len__(self):
       ...:         return len(self.addresses)
       ...:
       ...:     def __getitem__(self, index):
       ...:         return self.addresses[index]
       ...:


Метод __len__ вызывается функцией len:

.. code:: python

    In [2]: net1 = Network('10.1.1.192/30')

    In [3]: len(net1)
    Out[3]: 2


А метод __getitem__ при обращении по индексу таким образом:

.. code:: python

    In [4]: net1[0]
    Out[4]: '10.1.1.193'

    In [5]: net1[1]
    Out[5]: '10.1.1.194'

    In [6]: net1[-1]
    Out[6]: '10.1.1.194'

Метод __getitem__ отвечает не только обращение по индексу, но и за срезы:

.. code:: python

    In [7]: net1 = Network('10.1.1.192/28')

    In [8]: net1[0]
    Out[8]: '10.1.1.193'

    In [9]: net1[3:7]
    Out[9]: ['10.1.1.196', '10.1.1.197', '10.1.1.198', '10.1.1.199']

    In [10]: net1[3:]
    Out[10]:
    ['10.1.1.196',
     '10.1.1.197',
     '10.1.1.198',
     '10.1.1.199',
     '10.1.1.200',
     '10.1.1.201',
     '10.1.1.202',
     '10.1.1.203',
     '10.1.1.204',
     '10.1.1.205',
     '10.1.1.206']

Так как в данном случае, внутри метода __getitem__ используется список,
ошибки отрабатывают корректно автоматически:

.. code:: python

    In [11]: net1[100]
    ---------------------------------------------------------------------------
    IndexError                                Traceback (most recent call last)
    <ipython-input-11-09ca84e34cb6> in <module>
    ----> 1 net1[100]

    <ipython-input-2-bc213b4a03ca> in __getitem__(self, index)
         12
         13     def __getitem__(self, index):
    ---> 14         return self.addresses[index]
         15

    IndexError: list index out of range

    In [12]: net1['a']
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-12-facd90673864> in <module>
    ----> 1 net1['a']

    <ipython-input-2-bc213b4a03ca> in __getitem__(self, index)
         12
         13     def __getitem__(self, index):
    ---> 14         return self.addresses[index]
         15

    TypeError: list indices must be integers or slices, not str


Реализация остальных методов протокола последовательности вынесена в задания раздела:

* __contains__ - этот метод отвечает за проверку наличия элемента в 
  последовательности ``'10.1.1.198' in net1``. Если в объекте не определен этот метод,
  наличие элемента проверяется перебором элементов с помощью __iter__, а если и его нет
  перевором индексов с __getitem__.
* __reversed__ - используется встроенной функцией reversed. Этот метод как правило, 
  лучше не создавать и полагаться на то, что функция reversed при отсутствии 
  метода __reversed__ будет использовать методы __len__ и __getitem__.
* index - возвращает индекс первого элемента, значение которого равно указаному. 
  Работает полностью аналогично методу index в списках и кортежах.
* count - возвращает количество значений. Работает полностью аналогично методу 
  count в списках и кортежах.

