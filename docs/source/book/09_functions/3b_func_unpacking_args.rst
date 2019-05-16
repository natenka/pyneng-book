Распаковка аргументов
---------------------

В Python выражения ``*args`` и ``**kwargs`` позволяют выполнять ещё одну
задачу - **распаковку аргументов**.

До сих пор мы вызывали все функции вручную. И, соответственно,
передавали все нужные аргументы.

Но в реальной жизни, как правило, данные необходимо передавать в функцию
программно. И часто данные идут в виде какого-то объекта Python.

Распаковка позиционных аргументов
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Например, при форматировании строк часто надо передать методу format
несколько аргументов. И часто эти аргументы уже находятся в списке или
кортеже. Чтобы их передать методу format, приходится использовать
индексы таким образом:

.. code:: python

    In [1]: items = [1,2,3]

    In [2]: print('One: {}, Two: {}, Three: {}'.format(items[0], items[1], items[2]))
    One: 1, Two: 2, Three: 3

Но, вместо этого, можно воспользоваться распаковкой аргументов и сделать
так:

.. code:: python

    In [4]: items = [1,2,3]

    In [5]: print('One: {}, Two: {}, Three: {}'.format(*items))
    One: 1, Two: 2, Three: 3

Еще один пример - функция config\_interface (файл
func\_args\_unpacking.py):

.. code:: python

    def config_interface(intf_name, ip_address, cidr_mask):
        interface = 'interface {}'
        no_shut = 'no shutdown'
        ip_addr = 'ip address {} {}'
        result = []
        result.append(interface.format(intf_name))
        result.append(no_shut)

        mask_bits = int(cidr_mask.split('/')[-1])
        bin_mask = '1'*mask_bits + '0'*(32-mask_bits)
        dec_mask = [str(int(bin_mask[i:i+8], 2)) for i in range(0,25,8)]
        dec_mask_str = '.'.join(dec_mask)

        result.append(ip_addr.format(ip_address, dec_mask_str))
        return result

Функция ожидает как аргумент:

* intf\_name - имя интерфейса
* ip\_address - IP-адрес
* cidr\_mask - маску в формате CIDR (допускается и формат /24, и просто 24)

На выходе она выдает список строк для настройки интерфейса.

Например:

.. code:: python

    In [1]: config_interface('Fa0/1', '10.0.1.1', '/25')
    Out[1]: ['interface Fa0/1', 'no shutdown', 'ip address 10.0.1.1 255.255.255.128']

    In [2]: config_interface('Fa0/3', '10.0.0.1', '/18')
    Out[2]: ['interface Fa0/3', 'no shutdown', 'ip address 10.0.0.1 255.255.192.0']

    In [3]: config_interface('Fa0/3', '10.0.0.1', '/32')
    Out[3]: ['interface Fa0/3', 'no shutdown', 'ip address 10.0.0.1 255.255.255.255']

    In [4]: config_interface('Fa0/3', '10.0.0.1', '/30')
    Out[4]: ['interface Fa0/3', 'no shutdown', 'ip address 10.0.0.1 255.255.255.252']

    In [5]: config_interface('Fa0/3', '10.0.0.1', '30')
    Out[5]: ['interface Fa0/3', 'no shutdown', 'ip address 10.0.0.1 255.255.255.252']

Допустим, теперь нужно вызвать функцию и передать ей информацию, которая
была получена из другого источника, к примеру, из БД.

Например, список interfaces\_info, в котором находятся параметры для
настройки интерфейсов:

.. code:: python

    In [6]: interfaces_info = [['Fa0/1', '10.0.1.1', '/24'],
       ....:                    ['Fa0/2', '10.0.2.1', '/24'],
       ....:                    ['Fa0/3', '10.0.3.1', '/24'],
       ....:                    ['Fa0/4', '10.0.4.1', '/24'],
       ....:                    ['Lo0', '10.0.0.1', '/32']]

Если пройтись по списку в цикле и передавать вложенный список как
аргумент функции, возникнет ошибка:

.. code:: python

    In [7]: for info in interfaces_info:
       ....:     print(config_interface(info))
       ....:
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-5-f7d6a9d80d48> in <module>()
          1 for info in interfaces_info:
    ----> 2      print(config_interface(info))
          3

    TypeError: config_interface() missing 2 required positional arguments: 'ip_address' and 'cidr_mask'

Ошибка вполне логичная: функция ожидает три аргумента, а ей передан 1
аргумент - список.

В такой ситуации пригодится распаковка аргументов. Достаточно добавить
``*`` перед передачей списка как аргумента, и ошибки уже не будет:

.. code:: python

    In [8]: for info in interfaces_info:
      ....:     print(config_interface(*info))
      ....:
    ['interface Fa0/1', 'no shutdown', 'ip address 10.0.1.1 255.255.255.0']
    ['interface Fa0/2', 'no shutdown', 'ip address 10.0.2.1 255.255.255.0']
    ['interface Fa0/3', 'no shutdown', 'ip address 10.0.3.1 255.255.255.0']
    ['interface Fa0/4', 'no shutdown', 'ip address 10.0.4.1 255.255.255.0']
    ['interface Lo0', 'no shutdown', 'ip address 10.0.0.1 255.255.255.255']

Python сам 'распакует' список info и передаст в функцию элементы списка
как аргументы.

.. note::
    Таким же образом можно распаковывать и кортеж.

Распаковка ключевых аргументов
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Аналогичным образом можно распаковывать словарь, чтобы передать его как
ключевые аргументы.

Функция config\_to\_list (файл func\_args\_unpacking.py):

.. code:: python

    def config_to_list(cfg_file, delete_excl=True,
                       delete_empty=True, strip_end=True):
        result = []
        with open(cfg_file) as f:
            for line in f:
                if strip_end:
                    line = line.rstrip()
                if delete_empty and not line:
                    pass
                elif delete_excl and line.startswith('!'):
                    pass
                else:
                    result.append(line)
        return result

Функция берет файл с конфигурацией, убирает часть строк и возвращает
остальные строки как список.

Пример использования:

.. code:: python

    In [9]: config_to_list('r1.txt')
    Out[9]:
    ['service timestamps debug datetime msec localtime show-timezone year',
     'service timestamps log datetime msec localtime show-timezone year',
     'service password-encryption',
     'service sequence-numbers',
     'no ip domain lookup',
     'ip ssh version 2']

Список словарей ``cfg``, в которых указано имя файла и все аргументы:

.. code:: python

    In [10]: cfg = [dict(cfg_file='r1.txt', delete_excl=True, delete_empty=True, strip_end=True),
       ....:        dict(cfg_file='r2.txt', delete_excl=False, delete_empty=True, strip_end=True),
       ....:        dict(cfg_file='r3.txt', delete_excl=True, delete_empty=False, strip_end=True),
       ....:        dict(cfg_file='r4.txt', delete_excl=True, delete_empty=True, strip_end=False)]

Если передать словарь функции config\_to\_list, возникнет ошибка:

.. code:: python

    In [11]: for d in cfg:
       ....:     print(config_to_list(d))
       ....:
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-4-8d1e8defad71> in <module>()
          1 for d in cfg:
    ----> 2     print(config_to_list(d))
          3

    <ipython-input-1-6337ba2bfe7a> in config_to_list(cfg_file, delete_excl, delete_empty, strip_end)
          2                    delete_empty=True, strip_end=True):
          3     result = []
    ----> 4     with open( cfg_file ) as f:
          5         for line in f:
          6             if strip_end:

    TypeError: expected str, bytes or os.PathLike object, not dict

Ошибка такая, так как все параметры, кроме имени файла, опциональны. И
на стадии открытия файла возникает ошибка, так как вместо файла передан
словарь.

Если добавить ``**`` перед передачей словаря функции, функция нормально
отработает:

.. code:: python

    In [12]: for d in cfg:
        ...:     print(config_to_list(**d))
        ...:
    ['service timestamps debug datetime msec localtime show-timezone year', 'service timestamps log datetime msec localtime show-timezone year', 'service password-encryption', 'service sequence-numbers', 'no ip domain lookup', 'ip ssh version 2']
    ['!', 'service timestamps debug datetime msec localtime show-timezone year', 'service timestamps log datetime msec localtime show-timezone year', 'service password-encryption', 'service sequence-numbers', '!', 'no ip domain lookup', '!', 'ip ssh version 2', '!']
    ['service timestamps debug datetime msec localtime show-timezone year', 'service timestamps log datetime msec localtime show-timezone year', 'service password-encryption', 'service sequence-numbers', '', '', '', 'ip ssh version 2', '']
    ['service timestamps debug datetime msec localtime show-timezone year\n', 'service timestamps log datetime msec localtime show-timezone year\n', 'service password-encryption\n', 'service sequence-numbers\n', 'no ip domain lookup\n', 'ip ssh version 2\n']

Python распаковывает словарь и передает его в функцию как ключевые
аргументы.
