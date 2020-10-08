Флаги
-----

При использовании функций или создании скомпилированного регулярного
выражения можно указывать дополнительные флаги, которые влияют на
поведение регулярного выражения.

Модуль re поддерживает такие флаги (в скобках короткий вариант
обозначения флага): 

* re.ASCII (re.A) 
* re.IGNORECASE (re.I) 
* re.MULTILINE (re.M) 
* re.DOTALL (re.S) 
* re.VERBOSE (re.X) 
* re.LOCALE (re.L) 
* re.DEBUG

В этом подразделе для примера рассматривается флаг re.DOTALL. Информация
об остальных флагах доступна в
`документации <https://docs.python.org/3/library/re.html#re.A>`__.

re.DOTALL
^^^^^^^^^

С помощью регулярных выражений можно работать и с многострочной строкой.

Например, из строки sh_cdp надо получить имя устройства, платформу и IOS:

.. code:: python

    In [2]: sh_cdp = '''
       ...: Device ID: SW2
       ...: Entry address(es):
       ...:   IP address: 10.1.1.2
       ...: Platform: cisco WS-C2960-8TC-L,  Capabilities: Switch IGMP
       ...: Interface: GigabitEthernet1/0/16,  Port ID (outgoing port): GigabitEthernet0/1
       ...: Holdtime : 164 sec
       ...:
       ...: Version :
       ...: Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 12.2(55)SE9, RELEASE SOFTWARE (fc1)
       ...: Technical Support: http://www.cisco.com/techsupport
       ...: Copyright (c) 1986-2014 by Cisco Systems, Inc.
       ...: Compiled Mon 03-Mar-14 22:53 by prod_rel_team
       ...:
       ...: advertisement version: 2
       ...: VTP Management Domain: ''
       ...: Native VLAN: 1
       ...: Duplex: full
       ...: Management address(es):
       ...:   IP address: 10.1.1.2
       ...: '''

Конечно, в этом случае можно разделить строку на части и работать с
каждой строкой отдельно, но можно получить нужные данные и без разделения.

В этом выражении описаны строки с нужными данными:

.. code:: python

    In [3]: regex = r'Device ID: (\S+).+Platform: \w+ (\S+),.+Cisco IOS Software.+ Version (\S+),'

В таком случае, совпадения не будет, потому что по умолчанию точка означает
любой символ, кроме перевода строки:

.. code:: python


    In [4]: print(re.search(regex, sh_cdp))
    None

Изменить поведение по умолчанию, можно с помощью флага re.DOTALL:

.. code:: python

    In [5]: match = re.search(regex, sh_cdp, re.DOTALL)

    In [6]: match.groups()
    Out[6]: ('SW2', 'WS-C2960-8TC-L', '12.2(55)SE9')

Так как теперь в точку входит и перевод строки, комбинация ``.+`` захватывает все,
между нужными данными.

Теперь попробуем с помощью этого регулярного выражения, получить информацию про 
всех соседей из файла sh_cdp_neighbors_sw1.txt (вывод сокращен).

::

    SW1#show cdp neighbors detail
    -------------------------
    Device ID: SW2
    Entry address(es):
      IP address: 10.1.1.2
    Platform: cisco WS-C2960-8TC-L,  Capabilities: Switch IGMP
    Interface: GigabitEthernet1/0/16,  Port ID (outgoing port): GigabitEthernet0/1
    Holdtime : 164 sec

    Version :
    Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 12.2(55)SE9, RELEASE SOFTWARE (fc1)
    Technical Support: http://www.cisco.com/techsupport

    -------------------------
    Device ID: R1
    Entry address(es):
      IP address: 10.1.1.1
    Platform: Cisco 3825,  Capabilities: Router Switch IGMP
    Interface: GigabitEthernet1/0/22,  Port ID (outgoing port): GigabitEthernet0/0
    Holdtime : 156 sec

    Version :
    Cisco IOS Software, 3800 Software (C3825-ADVENTERPRISEK9-M), Version 12.4(24)T1, RELEASE SOFTWARE (fc3)
    Technical Support: http://www.cisco.com/techsupport

    -------------------------
    Device ID: R2
    Entry address(es):
      IP address: 10.2.2.2
    Platform: Cisco 2911,  Capabilities: Router Switch IGMP
    Interface: GigabitEthernet1/0/21,  Port ID (outgoing port): GigabitEthernet0/0
    Holdtime : 156 sec

    Version :
    Cisco IOS Software, 2900 Software (C3825-ADVENTERPRISEK9-M), Version 15.2(2)T1, RELEASE SOFTWARE (fc3)
    Technical Support: http://www.cisco.com/techsupport


Поиск всех совпадений с регулярным выражением:

.. code:: python

    In [7]: with open('sh_cdp_neighbors_sw1.txt') as f:
       ...:     sh_cdp = f.read()
       ...:

    In [8]: regex = r'Device ID: (\S+).+Platform: \w+ (\S+),.+Cisco IOS Software.+ Version (\S+),'

    In [9]: match = re.finditer(regex, sh_cdp, re.DOTALL)

    In [10]: for m in match:
        ...:     print(m.groups())
        ...:
    ('SW2', '2911', '15.2(2)T1')

На первый взгляд, кажется, что вместо трех устройств, в вывод попало только одно.
Однако, если присмотреться к результатам, окажется, что кортеже находится 
Device ID от первого соседа, а платформа и IOS от последнего.

Короткий вариант вывода, чтобы легче было ориентироваться в результатах:

::

    Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
    SW2              Gi 1/0/16         171              R S   C2960     Gi 0/1
    R1               Gi 1/0/22         158              R     C3825     Gi 0/0
    R2               Gi 1/0/21         177              R     C2911     Gi 0/0

Так получилось из-за того, что между нужными частями вывода, указана комбинация ``.+``.
Без флага ``re.DOTALL`` такое выражение захватило бы все до перевода строки, но
с флагом оно захватывает максимально длинный кусок текста, так как ``+`` жадный.
В итоге регулярное выражение описывает строку от первого Device ID до последнего 
места где встречается ``Cisco IOS Software.+ Version``.

Такая ситуация возникает очень часто при использовании ``re.DOTALL`` и чтобы исправить ее,
надо не забыть отключить жадность:

.. code:: python

    In [10]: regex = r'Device ID: (\S+).+?Platform: \w+ (\S+),.+?Cisco IOS Software.+? Version (\S+),'

    In [11]: match = re.finditer(regex, sh_cdp, re.DOTALL)

    In [12]: for m in match:
        ...:     print(m.groups())
        ...:
    ('SW2', 'WS-C2960-8TC-L', '12.2(55)SE9')
    ('R1', '3825', '12.4(24)T1')
    ('R2', '2911', '15.2(2)T1')


