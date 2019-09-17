Функция findall
----------------

Функция ``findall()``: 

* используется для поиска всех непересекающихся совпадений в шаблоне 
* возвращает:

  * список строк, которые описаны регулярным выражением,
    если в регулярном выражении нет групп 
  * список строк, которые совпали с регулярным выражением в группе, если в
    регулярном выражении одна группа 
  * список кортежей, в которых находятся строки,
    которые совпали с выражением в группе, если групп несколько

Рассмотрим работу findall на примере вывода команды sh mac
address-table:

.. code:: python

    In [2]: mac_address_table = open('CAM_table.txt').read()

    In [3]: print(mac_address_table)
    sw1#sh mac address-table
              Mac Address Table
    -------------------------------------------

    Vlan    Mac Address       Type        Ports
    ----    -----------       --------    -----
     100    a1b2.ac10.7000    DYNAMIC     Gi0/1
     200    a0d4.cb20.7000    DYNAMIC     Gi0/2
     300    acb4.cd30.7000    DYNAMIC     Gi0/3
     100    a2bb.ec40.7000    DYNAMIC     Gi0/4
     500    aa4b.c550.7000    DYNAMIC     Gi0/5
     200    a1bb.1c60.7000    DYNAMIC     Gi0/6
     300    aa0b.cc70.7000    DYNAMIC     Gi0/7

Первый пример - регулярное выражение без групп. В этом случае findall
возвращает список строк, которые совпали с регулярным выражением.

Например, с помощью findall можно получить список строк с соответствиями
vlan - mac - interface и избавиться от заголовка в выводе команды:

.. code:: python

    In [4]: re.findall(r'\d+ +\S+ +\w+ +\S+', mac_address_table)
    Out[4]:
    ['100    a1b2.ac10.7000    DYNAMIC     Gi0/1',
     '200    a0d4.cb20.7000    DYNAMIC     Gi0/2',
     '300    acb4.cd30.7000    DYNAMIC     Gi0/3',
     '100    a2bb.ec40.7000    DYNAMIC     Gi0/4',
     '500    aa4b.c550.7000    DYNAMIC     Gi0/5',
     '200    a1bb.1c60.7000    DYNAMIC     Gi0/6',
     '300    aa0b.cc70.7000    DYNAMIC     Gi0/7']

Обратите внимание, что findall возвращает список строк, а не объект
Match.

Как только в регулярном выражении появляется группа, findall ведет
себя по-другому.
Если в выражении используется одна группа, findall возвращает список
строк, которые совпали с выражением в группе:

.. code:: python

    In [5]: re.findall(r'\d+ +(\S+) +\w+ +\S+', mac_address_table)
    Out[5]:
    ['a1b2.ac10.7000',
     'a0d4.cb20.7000',
     'acb4.cd30.7000',
     'a2bb.ec40.7000',
     'aa4b.c550.7000',
     'a1bb.1c60.7000',
     'aa0b.cc70.7000']

При этом findall ищет совпадение всей строки, но возвращает результат,
похожий на метод groups() в объекте Match.

Если же групп несколько, findall вернет список кортежей:

.. code:: python

    In [6]: re.findall(r'(\d+) +(\S+) +\w+ +(\S+)', mac_address_table)
    Out[6]:
    [('100', 'a1b2.ac10.7000', 'Gi0/1'),
     ('200', 'a0d4.cb20.7000', 'Gi0/2'),
     ('300', 'acb4.cd30.7000', 'Gi0/3'),
     ('100', 'a2bb.ec40.7000', 'Gi0/4'),
     ('500', 'aa4b.c550.7000', 'Gi0/5'),
     ('200', 'a1bb.1c60.7000', 'Gi0/6'),
     ('300', 'aa0b.cc70.7000', 'Gi0/7')]

Если такие особенности работы функции findall мешают получить
необходимый результат, то лучше использовать функцию finditer, но иногда
такое поведение подходит и удобно использовать.

Пример использования findall в разборе лог-файла (файл
parse_log_findall.py):

.. code:: python

    import re

    regex = (r'Host \S+ '
             r'in vlan (\d+) '
             r'is flapping between port '
             r'(\S+) and port (\S+)')

    ports = set()

    with open('log.txt') as f:
        result = re.findall(regex, f.read())
        for vlan, port1, port2 in result:
            ports.add(port1)
            ports.add(port2)

    print('Петля между портами {} в VLAN {}'.format(', '.join(ports), vlan))

Результат:

::

    $ python parse_log_findall.py
    Петля между портами Gi0/19, Gi0/16, Gi0/24 в VLAN 10

