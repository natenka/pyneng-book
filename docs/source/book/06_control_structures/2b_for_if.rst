Совмещение for и if
~~~~~~~~~~~~~~~~~~~

Рассмотрим пример совмещения for и if.

Файл generate_access_port_config.py:

.. code-block:: python
   :linenos:

    access_template = ['switchport mode access',
                       'switchport access vlan',
                       'spanning-tree portfast',
                       'spanning-tree bpduguard enable']

    fast_int = {'access': { '0/12':10,
                            '0/14':11,
                            '0/16':17,
                            '0/17':150}}

    for intf, vlan in fast_int['access'].items():
        print('interface FastEthernet' + intf)
        for command in access_template:
            if command.endswith('access vlan'):
                print(' {} {}'.format(command, vlan))
            else:
                print(' {}'.format(command))

Комментарии к коду (в скобках номер строки):

* (11) В первом цикле for перебираются ключи и значения во вложенном словаре fast\_int['access']
* (11) Текущий ключ, на данный момент цикла, хранится в переменной intf
* (11) Текущее значение, на данный момент цикла, хранится в переменной vlan
* (12) Выводится строка interface FastEthernet с добавлением к ней номера интерфейса
* (13) Во втором цикле for перебираются команды из списка access_template
* Так как к команде switchport access vlan надо добавить номер VLAN:

  * (14) внутри второго цикла for проверяются команды
  * (14) если команда заканчивается на access vlan

    * (15) выводится команда, и к ней добавляется номер VLAN

  * (16-17) во всех остальных случаях просто выводится команда


Результат выполнения скрипта:

::

    $ python generate_access_port_config.py
    interface FastEthernet0/12
     switchport mode access
     switchport access vlan 10
     spanning-tree portfast
     spanning-tree bpduguard enable
    interface FastEthernet0/14
     switchport mode access
     switchport access vlan 11
     spanning-tree portfast
     spanning-tree bpduguard enable
    interface FastEthernet0/16
     switchport mode access
     switchport access vlan 17
     spanning-tree portfast
     spanning-tree bpduguard enable
    interface FastEthernet0/17
     switchport mode access
     switchport access vlan 150
     spanning-tree portfast
     spanning-tree bpduguard enable

