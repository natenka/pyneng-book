Ввод информации пользователем
-----------------------------

Иногда необходимо получить информацию от пользователя, например, запросить пароль.


Для получения информации от пользователя используется функция ``input``:

.. code:: python

    In [1]: print(input('Твой любимый протокол маршрутизации? '))
    Твой любимый протокол маршрутизации? OSPF
    OSPF

В данном случае информация тут же выводится пользователю, но
кроме этого, информация, которую ввел пользователь, может быть сохранена
в какую-то переменную и может использоваться далее в скрипте.

.. code:: python

    In [2]: protocol = input('Твой любимый протокол маршрутизации? ')
    Твой любимый протокол маршрутизации? OSPF

    In [3]: print(protocol)
    OSPF

В скобках обычно пишется какой-то вопрос, который уточняет, какую
информацию нужно ввести.

Запрос информации из скрипта (файл access_template_input.py):

.. code:: python

    interface = input('Enter interface type and number: ')
    vlan = input('Enter VLAN number: ')

    access_template = ['switchport mode access',
                       'switchport access vlan {}',
                       'switchport nonegotiate',
                       'spanning-tree portfast',
                       'spanning-tree bpduguard enable']

    print('\n' + '-' * 30)
    print('interface {}'.format(interface))
    print('\n'.join(access_template).format(vlan))

В первых двух строках запрашивается информация у пользователя.

Строка ``print('\n' + '-' * 30)`` используется для того,
чтобы визуально отделить запрос информации от вывода.

Выполнение скрипта:

::

    $ python access_template_input.py
    Enter interface type and number: Gi0/3
    Enter VLAN number: 55

    ------------------------------
    interface Gi0/3
    switchport mode access
    switchport access vlan 55
    switchport nonegotiate
    spanning-tree portfast
    spanning-tree bpduguard enable

