Вложенные for
~~~~~~~~~~~~~

Циклы for можно вкладывать друг в друга.

В этом примере в списке commands хранятся команды, которые надо
выполнить для каждого из интерфейсов в списке fast_int:

.. code:: python

    In [7]: commands = ['switchport mode access', 'spanning-tree portfast', 'spanning-tree bpduguard enable']
    In [8]: fast_int = ['0/1','0/3','0/4','0/7','0/9','0/10','0/11']

    In [9]: for intf in fast_int:
       ...:     print('interface FastEthernet {}'.format(intf))
       ...:     for command in commands:
       ...:         print(' {}'.format(command))
       ...:
    interface FastEthernet 0/1
     switchport mode access
     spanning-tree portfast
     spanning-tree bpduguard enable
    interface FastEthernet 0/3
     switchport mode access
     spanning-tree portfast
     spanning-tree bpduguard enable
    interface FastEthernet 0/4
     switchport mode access
     spanning-tree portfast
     spanning-tree bpduguard enable
    ...

Первый цикл for проходится по интерфейсам в списке fast_int, а второй
по командам в списке commands.
