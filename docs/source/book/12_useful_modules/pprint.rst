Модуль pprint
-------------

Модуль pprint позволяет красиво отображать объекты Python. При этом
сохраняется структура объекта и отображение, которое выводит pprint,
можно использовать для создания объекта.
Модуль pprint входит в стандартную библиотеку Python.

Самый простой вариант использования модуля - функция ``pprint``.
Например, словарь с вложенными словарями отобразится так:

.. code:: python

    In [6]: london_co = {'r1': {'hostname': 'london_r1', 'location': '21 New Globe Wal
       ...: k', 'vendor': 'Cisco', 'model': '4451', 'IOS': '15.4', 'IP': '10.255.0.1'}
       ...: , 'r2': {'hostname': 'london_r2', 'location': '21 New Globe Walk', 'vendor
       ...: ': 'Cisco', 'model': '4451', 'IOS': '15.4', 'IP': '10.255.0.2'}, 'sw1': {'
       ...: hostname': 'london_sw1', 'location': '21 New Globe Walk', 'vendor': 'Cisco
       ...: ', 'model': '3850', 'IOS': '3.6.XE', 'IP': '10.255.0.101'}}
       ...:

    In [7]: from pprint import pprint

    In [8]: pprint(london_co)
    {'r1': {'IOS': '15.4',
            'IP': '10.255.0.1',
            'hostname': 'london_r1',
            'location': '21 New Globe Walk',
            'model': '4451',
            'vendor': 'Cisco'},
     'r2': {'IOS': '15.4',
            'IP': '10.255.0.2',
            'hostname': 'london_r2',
            'location': '21 New Globe Walk',
            'model': '4451',
            'vendor': 'Cisco'},
     'sw1': {'IOS': '3.6.XE',
             'IP': '10.255.0.101',
             'hostname': 'london_sw1',
             'location': '21 New Globe Walk',
             'model': '3850',
             'vendor': 'Cisco'}}

Список списков:

.. code:: python

    In [13]: interfaces = [['FastEthernet0/0', '15.0.15.1', 'YES', 'manual', 'up', 'up
        ...: '], ['FastEthernet0/1', '10.0.1.1', 'YES', 'manual', 'up', 'up'], ['FastE
        ...: thernet0/2', '10.0.2.1', 'YES', 'manual', 'up', 'down']]
        ...:

    In [14]: pprint(interfaces)
    [['FastEthernet0/0', '15.0.15.1', 'YES', 'manual', 'up', 'up'],
     ['FastEthernet0/1', '10.0.1.1', 'YES', 'manual', 'up', 'up'],
     ['FastEthernet0/2', '10.0.2.1', 'YES', 'manual', 'up', 'down']]

Строка:

.. code:: python

    In [18]: tunnel
    Out[18]: '\ninterface Tunnel0\n ip address 10.10.10.1 255.255.255.0\n ip mtu 1416\n ip ospf hello-interval 5\n tunnel source FastEthernet1/0\n tunnel protection ipsec profile DMVPN\n'

    In [19]: pprint(tunnel)
    ('\n'
     'interface Tunnel0\n'
     ' ip address 10.10.10.1 255.255.255.0\n'
     ' ip mtu 1416\n'
     ' ip ospf hello-interval 5\n'
     ' tunnel source FastEthernet1/0\n'
     ' tunnel protection ipsec profile DMVPN\n')

Ограничение вложенности
~~~~~~~~~~~~~~~~~~~~~~~

У функции pprint есть дополнительный параметр depth, который позволяет
ограничивать глубину отображения структуры данных.

Например, есть такой словарь:

.. code:: python

    In [3]: result = {
       ...:  'interface Tunnel0': [' ip unnumbered Loopback0',
       ...:   ' tunnel mode mpls traffic-eng',
       ...:   ' tunnel destination 10.2.2.2',
       ...:   ' tunnel mpls traffic-eng priority 7 7',
       ...:   ' tunnel mpls traffic-eng bandwidth 5000',
       ...:   ' tunnel mpls traffic-eng path-option 10 dynamic',
       ...:   ' no routing dynamic'],
       ...:  'ip access-list standard LDP': [' deny   10.0.0.0 0.0.255.255',
       ...:   ' permit 10.0.0.0 0.255.255.255'],
       ...:  'router bgp 100': {' address-family vpnv4': ['  neighbor 10.2.2.2 activat
       ...: e',
       ...:    '  neighbor 10.2.2.2 send-community both',
       ...:    '  exit-address-family'],
       ...:   ' bgp bestpath igp-metric ignore': [],
       ...:   ' bgp log-neighbor-changes': [],
       ...:   ' neighbor 10.2.2.2 next-hop-self': [],
       ...:   ' neighbor 10.2.2.2 remote-as 100': [],
       ...:   ' neighbor 10.2.2.2 update-source Loopback0': [],
       ...:   ' neighbor 10.4.4.4 remote-as 40': []},
       ...:  'router ospf 1': [' mpls ldp autoconfig area 0',
       ...:   ' mpls traffic-eng router-id Loopback0',
       ...:   ' mpls traffic-eng area 0',
       ...:   ' network 10.0.0.0 0.255.255.255 area 0']}
       ...:

Можно отобразить только ключи, указав глубину равной 1:

.. code:: python

    In [5]: pprint(result, depth=1)
    {'interface Tunnel0': [...],
     'ip access-list standard LDP': [...],
     'router bgp 100': {...},
     'router ospf 1': [...]}

Скрытые уровни вложенности заменяются ``...``.

Если указать глубину равной 2, отобразится следующий уровень:

.. code:: python

    In [6]: pprint(result, depth=2)
    {'interface Tunnel0': [' ip unnumbered Loopback0',
                           ' tunnel mode mpls traffic-eng',
                           ' tunnel destination 10.2.2.2',
                           ' tunnel mpls traffic-eng priority 7 7',
                           ' tunnel mpls traffic-eng bandwidth 5000',
                           ' tunnel mpls traffic-eng path-option 10 dynamic',
                           ' no routing dynamic'],
     'ip access-list standard LDP': [' deny   10.0.0.0 0.0.255.255',
                                     ' permit 10.0.0.0 0.255.255.255'],
     'router bgp 100': {' address-family vpnv4': [...],
                        ' bgp bestpath igp-metric ignore': [],
                        ' bgp log-neighbor-changes': [],
                        ' neighbor 10.2.2.2 next-hop-self': [],
                        ' neighbor 10.2.2.2 remote-as 100': [],
                        ' neighbor 10.2.2.2 update-source Loopback0': [],
                        ' neighbor 10.4.4.4 remote-as 40': []},
     'router ospf 1': [' mpls ldp autoconfig area 0',
                       ' mpls traffic-eng router-id Loopback0',
                       ' mpls traffic-eng area 0',
                       ' network 10.0.0.0 0.255.255.255 area 0']}

pformat
~~~~~~~

pformat - это функция, которая отображает результат в виде строки. Ее
удобно использовать, если необходимо записать структуру данных в
какой-то файл, например, для логирования.

.. code:: python

    In [15]: from pprint import pformat

    In [16]: formatted_result = pformat(result)

    In [17]: print(formatted_result)
    {'interface Tunnel0': [' ip unnumbered Loopback0',
                           ' tunnel mode mpls traffic-eng',
                           ' tunnel destination 10.2.2.2',
                           ' tunnel mpls traffic-eng priority 7 7',
                           ' tunnel mpls traffic-eng bandwidth 5000',
                           ' tunnel mpls traffic-eng path-option 10 dynamic',
                           ' no routing dynamic'],
     'ip access-list standard LDP': [' deny   10.0.0.0 0.0.255.255',
                                     ' permit 10.0.0.0 0.255.255.255'],
     'router bgp 100': {' address-family vpnv4': ['  neighbor 10.2.2.2 activate',
                                                  '  neighbor 10.2.2.2 '
                                                  'send-community both',
                                                  '  exit-address-family'],
                        ' bgp bestpath igp-metric ignore': [],
                        ' bgp log-neighbor-changes': [],
                        ' neighbor 10.2.2.2 next-hop-self': [],
                        ' neighbor 10.2.2.2 remote-as 100': [],
                        ' neighbor 10.2.2.2 update-source Loopback0': [],
                        ' neighbor 10.4.4.4 remote-as 40': []},
     'router ospf 1': [' mpls ldp autoconfig area 0',
                       ' mpls traffic-eng router-id Loopback0',
                       ' mpls traffic-eng area 0',
                       ' network 10.0.0.0 0.255.255.255 area 0']}


Сортировка словарей
~~~~~~~~~~~~~~~~~~~

pprint сортирует словари при выводе, что не всегда удобно. Начиная с
версии Python 3.8, появилась возможность отключить сортировку:

.. code:: python

    In [3]: r1 = {"ios": "16.4", "hostname": "R1", "ip": "10.1.1.1", "vendor": "Cisco IOS"}

    In [4]: pprint(r1)
    {'hostname': 'R1', 'ios': '16.4', 'ip': '10.1.1.1', 'vendor': 'Cisco IOS'}

    In [5]: pprint(r1, sort_dicts=False)
    {'ios': '16.4', 'hostname': 'R1', 'ip': '10.1.1.1', 'vendor': 'Cisco IOS'}

