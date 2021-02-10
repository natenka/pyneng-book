Модуль tabulate
---------------

tabulate - это модуль, который позволяет красиво отображать
табличные данные. Он не входит в стандартную библиотеку Python,
поэтому tabulate нужно установить:

::

    pip install tabulate

Модуль поддерживает такие типы табличных данных:

* список списков (в общем случае iterable of iterables)
* список словарей (или любой другой итерируемый объект со словарями). Ключи используются как имена столбцов
* словарь с итерируемыми объектами. Ключи используются как имена столбцов

Для генерации таблицы используется функция tabulate:

.. code:: python

    In [1]: from tabulate import tabulate

    In [2]: sh_ip_int_br = [('FastEthernet0/0', '15.0.15.1', 'up', 'up'),
       ...:  ('FastEthernet0/1', '10.0.12.1', 'up', 'up'),
       ...:  ('FastEthernet0/2', '10.0.13.1', 'up', 'up'),
       ...:  ('Loopback0', '10.1.1.1', 'up', 'up'),
       ...:  ('Loopback100', '100.0.0.1', 'up', 'up')]
       ...:

    In [4]: print(tabulate(sh_ip_int_br))
    ---------------  ---------  --  --
    FastEthernet0/0  15.0.15.1  up  up
    FastEthernet0/1  10.0.12.1  up  up
    FastEthernet0/2  10.0.13.1  up  up
    Loopback0        10.1.1.1   up  up
    Loopback100      100.0.0.1  up  up
    ---------------  ---------  --  --

headers
~~~~~~~

Параметр headers позволяет передавать дополнительный аргумент, в котором
указаны имена столбцов:

.. code:: python

    In [8]: columns = ['Interface', 'IP', 'Status', 'Protocol']

    In [9]: print(tabulate(sh_ip_int_br, headers=columns))
    Interface        IP         Status    Protocol
    ---------------  ---------  --------  ----------
    FastEthernet0/0  15.0.15.1  up        up
    FastEthernet0/1  10.0.12.1  up        up
    FastEthernet0/2  10.0.13.1  up        up
    Loopback0        10.1.1.1   up        up
    Loopback100      100.0.0.1  up        up

Достаточно часто первый набор данных - это заголовки. Тогда достаточно
указать headers равным "firstrow":

.. code:: python

    In [18]: data
    Out[18]:
    [('Interface', 'IP', 'Status', 'Protocol'),
     ('FastEthernet0/0', '15.0.15.1', 'up', 'up'),
     ('FastEthernet0/1', '10.0.12.1', 'up', 'up'),
     ('FastEthernet0/2', '10.0.13.1', 'up', 'up'),
     ('Loopback0', '10.1.1.1', 'up', 'up'),
     ('Loopback100', '100.0.0.1', 'up', 'up')]

    In [20]: print(tabulate(data, headers='firstrow'))
    Interface        IP         Status    Protocol
    ---------------  ---------  --------  ----------
    FastEthernet0/0  15.0.15.1  up        up
    FastEthernet0/1  10.0.12.1  up        up
    FastEthernet0/2  10.0.13.1  up        up
    Loopback0        10.1.1.1   up        up
    Loopback100      100.0.0.1  up        up

Если данные в виде списка словарей, надо указать headers равным "keys":

.. code:: python

    In [22]: list_of_dict
    Out[22]:
    [{'IP': '15.0.15.1',
      'Interface': 'FastEthernet0/0',
      'Protocol': 'up',
      'Status': 'up'},
     {'IP': '10.0.12.1',
      'Interface': 'FastEthernet0/1',
      'Protocol': 'up',
      'Status': 'up'},
     {'IP': '10.0.13.1',
      'Interface': 'FastEthernet0/2',
      'Protocol': 'up',
      'Status': 'up'},
     {'IP': '10.1.1.1',
      'Interface': 'Loopback0',
      'Protocol': 'up',
      'Status': 'up'},
     {'IP': '100.0.0.1',
      'Interface': 'Loopback100',
      'Protocol': 'up',
      'Status': 'up'}]

    In [23]: print(tabulate(list_of_dict, headers='keys'))
    Interface        IP         Status    Protocol
    ---------------  ---------  --------  ----------
    FastEthernet0/0  15.0.15.1  up        up
    FastEthernet0/1  10.0.12.1  up        up
    FastEthernet0/2  10.0.13.1  up        up
    Loopback0        10.1.1.1   up        up
    Loopback100      100.0.0.1  up        up

Отображение словаря:

.. code:: python

    In [6]: vlans = {"sw1": [10, 20, 30, 40], "sw2": [1, 2, 10], "sw3": [1, 2, 3, 4, 5, 10, 11, 12]}

    In [7]: print(tabulate(vlans, headers="keys"))
      sw1    sw2    sw3
    -----  -----  -----
       10      1      1
       20      2      2
       30     10      3
       40             4
                      5
                     10
                     11
                     12


Стиль таблицы
~~~~~~~~~~~~~

tabulate поддерживает разные стили отображения таблицы.

Формат grid:

::

    In [24]: print(tabulate(list_of_dict, headers='keys', tablefmt="grid"))
    +-----------------+-----------+----------+------------+
    | Interface       | IP        | Status   | Protocol   |
    +=================+===========+==========+============+
    | FastEthernet0/0 | 15.0.15.1 | up       | up         |
    +-----------------+-----------+----------+------------+
    | FastEthernet0/1 | 10.0.12.1 | up       | up         |
    +-----------------+-----------+----------+------------+
    | FastEthernet0/2 | 10.0.13.1 | up       | up         |
    +-----------------+-----------+----------+------------+
    | Loopback0       | 10.1.1.1  | up       | up         |
    +-----------------+-----------+----------+------------+
    | Loopback100     | 100.0.0.1 | up       | up         |
    +-----------------+-----------+----------+------------+

Таблица в формате Markdown:

::

    In [25]: print(tabulate(list_of_dict, headers='keys', tablefmt='pipe'))
    | Interface       | IP        | Status   | Protocol   |
    |:----------------|:----------|:---------|:-----------|
    | FastEthernet0/0 | 15.0.15.1 | up       | up         |
    | FastEthernet0/1 | 10.0.12.1 | up       | up         |
    | FastEthernet0/2 | 10.0.13.1 | up       | up         |
    | Loopback0       | 10.1.1.1  | up       | up         |
    | Loopback100     | 100.0.0.1 | up       | up         |

Таблица в формате HTML:

::

    In [26]: print(tabulate(list_of_dict, headers='keys', tablefmt='html'))
    <table>
    <thead>
    <tr><th>Interface      </th><th>IP       </th><th>Status  </th><th>Protocol  </th></tr>
    </thead>
    <tbody>
    <tr><td>FastEthernet0/0</td><td>15.0.15.1</td><td>up      </td><td>up        </td></tr>
    <tr><td>FastEthernet0/1</td><td>10.0.12.1</td><td>up      </td><td>up        </td></tr>
    <tr><td>FastEthernet0/2</td><td>10.0.13.1</td><td>up      </td><td>up        </td></tr>
    <tr><td>Loopback0      </td><td>10.1.1.1 </td><td>up      </td><td>up        </td></tr>
    <tr><td>Loopback100    </td><td>100.0.0.1</td><td>up      </td><td>up        </td></tr>
    </tbody>
    </table>

Выравнивание столбцов
~~~~~~~~~~~~~~~~~~~~~

Можно указывать выравнивание для столбцов:

.. code:: python

    In [27]: print(tabulate(list_of_dict, headers='keys', tablefmt='pipe', stralign='center'))
    |    Interface    |    IP     |  Status  |  Protocol  |
    |:---------------:|:---------:|:--------:|:----------:|
    | FastEthernet0/0 | 15.0.15.1 |    up    |     up     |
    | FastEthernet0/1 | 10.0.12.1 |    up    |     up     |
    | FastEthernet0/2 | 10.0.13.1 |    up    |     up     |
    |    Loopback0    | 10.1.1.1  |    up    |     up     |
    |   Loopback100   | 100.0.0.1 |    up    |     up     |

Обратите внимание, что тут не только столбцы отобразились с
выравниванием по центру, но и соответственно изменился синтаксис
Markdown.

