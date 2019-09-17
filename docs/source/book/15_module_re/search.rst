Функция search
--------------

Функция ``search()``: 

* используется для поиска подстроки, которая соответствует шаблону 
* возвращает объект Match, если подстрока найдена
* возвращает ``None``, если подстрока не найдена

Функция search подходит в том случае, когда надо найти только одно
совпадение в строке, например, когда регулярное выражение описывает всю
строку или часть строки.

Рассмотрим пример использования функции search в разборе лог-файла.

В файле log.txt находятся лог-сообщения с информацией о том, что один и
тот же MAC слишком быстро переучивается то на одном, то на другом
интерфейсе. Одна из причин таких сообщений - петля в сети.

Содержимое файла log.txt:

::

    %SW_MATM-4-MACFLAP_NOTIF: Host 01e2.4c18.0156 in vlan 10 is flapping between port Gi0/16 and port Gi0/24
    %SW_MATM-4-MACFLAP_NOTIF: Host 01e2.4c18.0156 in vlan 10 is flapping between port Gi0/16 and port Gi0/24
    %SW_MATM-4-MACFLAP_NOTIF: Host 01e2.4c18.0156 in vlan 10 is flapping between port Gi0/24 and port Gi0/19
    %SW_MATM-4-MACFLAP_NOTIF: Host 01e2.4c18.0156 in vlan 10 is flapping between port Gi0/24 and port Gi0/16

При этом MAC-адрес может прыгать между несколькими портами. В таком
случае очень важно знать, с каких портов прилетает MAC.

Попробуем вычислить, между какими портами и в каком VLAN образовалась
проблема.
Проверка регулярного выражения с одной строкой из log-файла:

.. code:: python

    In [1]: import re

    In [2]: log = '%SW_MATM-4-MACFLAP_NOTIF: Host 01e2.4c18.0156 in vlan 10 is flapping between port Gi0/16 and port Gi0/24'

    In [3]: match = re.search(r'Host \S+ '
       ...:                   r'in vlan (\d+) '
       ...:                   r'is flapping between port '
       ...:                   r'(\S+) and port (\S+)', log)
       ...:

Регулярное выражение для удобства чтения разбито на части. В нём есть три группы: 

* ``(\d+)`` - описывает номер VLAN 
* ``(\S+) and port (\S+)`` - в это выражение попадают номера портов

В итоге, в группы попали такие части строки:

.. code:: python

    In [4]: match.groups()
    Out[4]: ('10', 'Gi0/16', 'Gi0/24')

В итоговом скрипте файл log.txt обрабатывается построчно, и из каждой
строки собирается информация о портах. Так как порты могут
дублироваться, сразу добавляем их в множество, чтобы получить подборку
уникальных интерфейсов (файл parse_log_search.py):

.. literalinclude:: /pyneng-examples-exercises/examples/15_module_re/parse_log_search.py
  :language: python
  :linenos:

Результат выполнения скрипта такой:

::

    $ python parse_log_search.py
    Петля между портами Gi0/19, Gi0/24, Gi0/16 в VLAN 10

Обработка вывода show cdp neighbors detail
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Попробуем получить параметры устройств из вывода sh cdp neighbors
detail.

Пример вывода информации для одного соседа:

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
    Copyright (c) 1986-2014 by Cisco Systems, Inc.
    Compiled Mon 03-Mar-14 22:53 by prod_rel_team

    advertisement version: 2
    VTP Management Domain: ''
    Native VLAN: 1
    Duplex: full
    Management address(es):
      IP address: 10.1.1.2

Задача получить такие поля: 

* имя соседа (Device ID: SW2) 
* IP-адрес соседа (IP address: 10.1.1.2) 
* платформу соседа (Platform: cisco WS-C2960-8TC-L) 
* версию IOS (Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 12.2(55)SE9, RELEASE SOFTWARE (fc1))

И для удобства надо получить данные в виде словаря. Пример итогового
словаря для коммутатора SW2:

.. code:: python

    {'SW2': {'ip': '10.1.1.2',
             'platform': 'cisco WS-C2960-8TC-L',
             'ios': 'C2960 Software (C2960-LANBASEK9-M), Version 12.2(55)SE9'}}

Пример проверяется на файле sh_cdp_neighbors_sw1.txt.

Первый вариант решения (файл parse_sh_cdp_neighbors_detail_ver1.py):

.. literalinclude:: /pyneng-examples-exercises/examples/15_module_re/parse_sh_cdp_neighbors_detail_ver1.py
  :language: python
  :linenos:

Тут нужные строки отбираются с помощью метода строк startswith. И в
строке с помощью регулярного выражения получается требуемая часть
строки.
В итоге все собирается в словарь.

Результат выглядит так:

.. code:: python

    $ python parse_sh_cdp_neighbors_detail_ver1.py
    {'R1': {'ios': '3800 Software (C3825-ADVENTERPRISEK9-M), Version 12.4(24)T1',
            'ip': '10.1.1.1',
            'platform': 'Cisco 3825'},
     'R2': {'ios': '2900 Software (C3825-ADVENTERPRISEK9-M), Version 15.2(2)T1',
            'ip': '10.2.2.2',
            'platform': 'Cisco 2911'},
     'SW2': {'ios': 'C2960 Software (C2960-LANBASEK9-M), Version 12.2(55)SE9',
             'ip': '10.1.1.2',
             'platform': 'cisco WS-C2960-8TC-L'}}

Все получилось как нужно, но эту задачу можно решить более компактно.

Вторая версия решения (файл parse_sh_cdp_neighbors_detail_ver2.py):

.. literalinclude:: /pyneng-examples-exercises/examples/15_module_re/parse_sh_cdp_neighbors_detail_ver2.py
  :language: python
  :linenos:

Пояснения ко второму варианту: 

* в регулярном выражении описаны все варианты строк через знак или ``|`` 
* без проверки строки ищется совпадение 
* если совпадение найдено, проверяется метод lastgroup 
* метод lastgroup возвращает имя последней именованной группы в регулярном 
  выражении, для которой было найдено совпадение 
* если было найдено совпадение для группы device, в переменную device записывается значение,
  которое попало в эту группу 
* иначе в словарь записывается соответствие 'имя группы': соответствующее значение

Результат будет таким же:

.. code:: python

    $ python parse_sh_cdp_neighbors_detail_ver2.py
    {'R1': {'ios': '3800 Software (C3825-ADVENTERPRISEK9-M), Version 12.4(24)T1',
            'ip': '10.1.1.1',
            'platform': 'Cisco 3825'},
     'R2': {'ios': '2900 Software (C3825-ADVENTERPRISEK9-M), Version 15.2(2)T1',
            'ip': '10.2.2.2',
            'platform': 'Cisco 2911'},
     'SW2': {'ios': 'C2960 Software (C2960-LANBASEK9-M), Version 12.2(55)SE9',
             'ip': '10.1.1.2',
             'platform': 'cisco WS-C2960-8TC-L'}}

