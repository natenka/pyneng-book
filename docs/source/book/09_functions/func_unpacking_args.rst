.. _unpacking_args:

Распаковка аргументов
---------------------

В Python выражения ``*args`` и ``**kwargs`` позволяют выполнять ещё одну
задачу - **распаковку аргументов**.

До сих пор мы вызывали все функции вручную. И соответственно
передавали все нужные аргументы.

В реальности, как правило, данные необходимо передавать в функцию
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

Вместо этого, можно воспользоваться распаковкой аргументов и сделать так:

.. code:: python

    In [4]: items = [1,2,3]

    In [5]: print('One: {}, Two: {}, Three: {}'.format(*items))
    One: 1, Two: 2, Three: 3


Еще один пример - функция config_interface (файл
func_config_interface_unpacking.py):

.. code:: python

    In [8]: def config_interface(intf_name, ip_address, mask):
        ..:     interface = f'interface {intf_name}'
        ..:     no_shut = 'no shutdown'
        ..:     ip_addr = f'ip address {ip_address} {mask}'
        ..:     result = [interface, no_shut, ip_addr]
        ..:     return result
        ..:


Функция ожидает такие аргументы:

* intf_name - имя интерфейса
* ip_address - IP-адрес
* mask - маска

Функция возвращает список строк для настройки интерфейса:

.. code:: python


    In [9]: config_interface('Fa0/1', '10.0.1.1', '255.255.255.0')
    Out[9]: ['interface Fa0/1', 'no shutdown', 'ip address 10.0.1.1 255.255.255.0']

    In [11]: config_interface('Fa0/10', '10.255.4.1', '255.255.255.0')
    Out[11]: ['interface Fa0/10', 'no shutdown', 'ip address 10.255.4.1 255.255.255.0']


Допустим, нужно вызвать функцию и передать ей информацию, которая
была получена из другого источника, к примеру, из БД.

Например, список interfaces_info, в котором находятся параметры для
настройки интерфейсов:

.. code:: python

    In [14]: interfaces_info = [['Fa0/1', '10.0.1.1', '255.255.255.0'],
        ...:                    ['Fa0/2', '10.0.2.1', '255.255.255.0'],
        ...:                    ['Fa0/3', '10.0.3.1', '255.255.255.0'],
        ...:                    ['Fa0/4', '10.0.4.1', '255.255.255.0'],
        ...:                    ['Lo0', '10.0.0.1', '255.255.255.255']]
        ...:


Если пройтись по списку в цикле и передавать вложенный список как
аргумент функции, возникнет ошибка:

.. code:: python

    In [15]: for info in interfaces_info:
        ...:     print(config_interface(info))
        ...:
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-15-d34ced60c796> in <module>
          1 for info in interfaces_info:
    ----> 2     print(config_interface(info))
          3

    TypeError: config_interface() missing 2 required positional arguments: 'ip_address' and 'mask'

Ошибка вполне логичная: функция ожидает три аргумента, а ей передан 1
аргумент - список.

В такой ситуации пригодится распаковка аргументов. Достаточно добавить
``*`` перед передачей списка как аргумента, и ошибки уже не будет:

.. code:: python

    In [16]: for info in interfaces_info:
        ...:     print(config_interface(*info))
        ...:
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

Функция check_passwd (файл func_check_passwd_optional_param_2.py):

.. code:: python

    In [19]: def check_passwd(username, password, min_length=8, check_username=True):
        ...:     if len(password) < min_length:
        ...:         print('Пароль слишком короткий')
        ...:         return False
        ...:     elif check_username and username in password:
        ...:         print('Пароль содержит имя пользователя')
        ...:         return False
        ...:     else:
        ...:         print(f'Пароль для пользователя {username} прошел все проверки')
        ...:         return True
        ...:


Список словарей ``username_passwd``, в которых указано имя пользователя и пароль:

.. code:: python

    In [20]: username_passwd = [{'username': 'cisco', 'password': 'cisco'},
        ...:                    {'username': 'nata', 'password': 'natapass'},
        ...:                    {'username': 'user', 'password': '123456789'}]

Если передать словарь функции check_passwd, возникнет ошибка:

.. code:: python

    In [21]: for data in username_passwd:
        ...:     check_passwd(data)
        ...:
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-21-ad848f85c77f> in <module>
          1 for data in username_passwd:
    ----> 2     check_passwd(data)
          3

    TypeError: check_passwd() missing 1 required positional argument: 'password'


Ошибка такая, так как функция восприняла словарь как один аргумент и считает что ей не хватает только
аргумента password.

Если добавить ``**`` перед передачей словаря функции, функция нормально
отработает:

.. code:: python

    In [22]: for data in username_passwd:
        ...:     check_passwd(**data)
        ...:
    Пароль слишком короткий
    Пароль содержит имя пользователя
    Пароль для пользователя user прошел все проверки

    In [23]: for data in username_passwd:
        ...:     print(data)
        ...:     check_passwd(**data)
        ...:
    {'username': 'cisco', 'password': 'cisco'}
    Пароль слишком короткий
    {'username': 'nata', 'password': 'natapass'}
    Пароль содержит имя пользователя
    {'username': 'user', 'password': '123456789'}
    Пароль для пользователя user прошел все проверки

Python распаковывает словарь и передает его в функцию как ключевые аргументы.
Запись ``check_passwd(**data)`` превращается в вызов вида ``check_passwd(username='cisco', password='cisco')``.

