Функция finditer
-----------------

Функция ``finditer()``: 

* используется для поиска всех непересекающихся совпадений в шаблоне 
* возвращает итератор с объектами Match
* finditer возвращает итератор даже в том случае, когда совпадение не найдено

Функция finditer отлично подходит для обработки тех команд, вывод
которых отображается столбцами. Например, sh ip int br, sh mac
address-table и др. В этом случае его можно применять ко всему выводу
команды.

Пример вывода sh ip int br:

.. code:: python

    In [8]: sh_ip_int_br = '''
       ...: R1#show ip interface brief
       ...: Interface             IP-Address      OK? Method Status           Protocol
       ...: FastEthernet0/0       15.0.15.1       YES manual up               up
       ...: FastEthernet0/1       10.0.12.1       YES manual up               up
       ...: FastEthernet0/2       10.0.13.1       YES manual up               up
       ...: FastEthernet0/3       unassigned      YES unset  up               up
       ...: Loopback0             10.1.1.1        YES manual up               up
       ...: Loopback100           100.0.0.1       YES manual up               up
       ...: '''

Регулярное выражение для обработки вывода:

.. code:: python

    In [9]: result = re.finditer(r'(\S+) +'
       ...:                      r'([\d.]+) +'
       ...:                      r'\w+ +\w+ +'
       ...:                      r'(up|down|administratively down) +'
       ...:                      r'(up|down)',
       ...:                      sh_ip_int_br)
       ...:

В переменной result находится итератор:

.. code:: python

    In [12]: result
    Out[12]: <callable_iterator at 0xb583f46c>

В итераторе находятся объекты Match:

.. code:: python

    In [16]: groups = []

    In [18]: for match in result:
        ...:     print(match)
        ...:     groups.append(match.groups())
        ...:
    <_sre.SRE_Match object; span=(103, 171), match='FastEthernet0/0       15.0.15.1       YES manual >
    <_sre.SRE_Match object; span=(172, 240), match='FastEthernet0/1       10.0.12.1       YES manual >
    <_sre.SRE_Match object; span=(241, 309), match='FastEthernet0/2       10.0.13.1       YES manual >
    <_sre.SRE_Match object; span=(379, 447), match='Loopback0             10.1.1.1        YES manual >
    <_sre.SRE_Match object; span=(448, 516), match='Loopback100           100.0.0.1       YES manual >'

Теперь в списке groups находятся кортежи со строками, которые попали в
группы:

.. code:: python

    In [19]: groups
    Out[19]:
    [('FastEthernet0/0', '15.0.15.1', 'up', 'up'),
     ('FastEthernet0/1', '10.0.12.1', 'up', 'up'),
     ('FastEthernet0/2', '10.0.13.1', 'up', 'up'),
     ('Loopback0', '10.1.1.1', 'up', 'up'),
     ('Loopback100', '100.0.0.1', 'up', 'up')]

Аналогичный результат можно получить с помощью генератора списков:

.. code:: python

    In [20]: regex = r'(\S+) +([\d.]+) +\w+ +\w+ +(up|down|administratively down) +(up|down)'

    In [21]: result = [match.groups() for match in re.finditer(regex, sh_ip_int_br)]

    In [22]: result
    Out[22]:
    [('FastEthernet0/0', '15.0.15.1', 'up', 'up'),
     ('FastEthernet0/1', '10.0.12.1', 'up', 'up'),
     ('FastEthernet0/2', '10.0.13.1', 'up', 'up'),
     ('Loopback0', '10.1.1.1', 'up', 'up'),
     ('Loopback100', '100.0.0.1', 'up', 'up')]

Теперь разберем тот же лог-файл, который использовался в подразделах
search и match.

В этом случае вывод можно не перебирать построчно, а передать все
содержимое файла (файл parse_log_finditer.py):

.. code:: python

    import re

    regex = (r'Host \S+ '
             r'in vlan (\d+) '
             r'is flapping between port '
             r'(\S+) and port (\S+)')

    ports = set()

    with open('log.txt') as f:
        for m in re.finditer(regex, f.read()):
            vlan = m.group(1)
            ports.add(m.group(2))
            ports.add(m.group(3))

    print('Петля между портами {} в VLAN {}'.format(', '.join(ports), vlan))

.. warning::

    В реальной жизни log-файл может быть очень большим. В таком случае,
    его лучше обрабатывать построчно.

Вывод будет таким же:

::

    $ python parse_log_finditer.py
    Петля между портами Gi0/19, Gi0/24, Gi0/16 в VLAN 10

Обработка вывода show cdp neighbors detail
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

С помощью finditer можно обработать вывод sh cdp neighbors detail, так
же, как и в подразделе re.search.

Скрипт почти полностью аналогичен варианту с re.search (файл
parse_sh_cdp_neighbors_detail_finditer.py):

.. code:: python

    import re
    from pprint import pprint


    def parse_cdp(filename):
        regex = (r'Device ID: (?P<device>\S+)'
                 r'|IP address: (?P<ip>\S+)'
                 r'|Platform: (?P<platform>\S+ \S+),'
                 r'|Cisco IOS Software, (?P<ios>.+), RELEASE')

        result = {}

        with open(filename) as f:
            match_iter = re.finditer(regex, f.read())
            for match in match_iter:
                if match.lastgroup == 'device':
                    device = match.group(match.lastgroup)
                    result[device] = {}
                elif device:
                    result[device][match.lastgroup] = match.group(match.lastgroup)

        return result

    pprint(parse_cdp('sh_cdp_neighbors_sw1.txt'))

Теперь совпадения ищутся во всем файле, а не в каждой строке отдельно:

.. code:: python

        with open(filename) as f:
            match_iter = re.finditer(regex, f.read())

Затем перебираются совпадения:

.. code:: python

        with open(filename) as f:
            match_iter = re.finditer(regex, f.read())
            for match in match_iter:

Остальное аналогично.

Результат будет таким:

.. code:: python

    $ python parse_sh_cdp_neighbors_detail_finditer.py
    {'R1': {'ios': '3800 Software (C3825-ADVENTERPRISEK9-M), Version 12.4(24)T1',
            'ip': '10.1.1.1',
            'platform': 'Cisco 3825'},
     'R2': {'ios': '2900 Software (C3825-ADVENTERPRISEK9-M), Version 15.2(2)T1',
            'ip': '10.2.2.2',
            'platform': 'Cisco 2911'},
     'SW2': {'ios': 'C2960 Software (C2960-LANBASEK9-M), Version 12.2(55)SE9',
             'ip': '10.1.1.2',
             'platform': 'cisco WS-C2960-8TC-L'}}

Хотя результат аналогичный, с finditer больше возможностей, так как
можно указывать не только то, что должно находиться в нужной строке, но
и в строках вокруг.

Например, можно точнее указать, какой именно IP-адрес надо взять:

::

    Device ID: SW2
    Entry address(es):
      IP address: 10.1.1.2
    Platform: cisco WS-C2960-8TC-L,  Capabilities: Switch IGMP

    ...

    Native VLAN: 1
    Duplex: full
    Management address(es):
      IP address: 10.1.1.2

Например, если нужно взять первый IP-адрес, можно так дополнить
регулярное выражение:

.. code:: python

    regex = (r'Device ID: (?P<device>\S+)'
             r'|Entry address.*\n +IP address: (?P<ip>\S+)'
             r'|Platform: (?P<platform>\S+ \S+),'
             r'|Cisco IOS Software, (?P<ios>.+), RELEASE')

