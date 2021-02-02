Объект Match
------------

В модуле re несколько функций возвращают объект Match, если было найдено
совпадение: 

* search 
* match 
* finditer возвращает итератор с объектами Match

В этом подразделе рассматриваются методы объекта Match.

Пример объекта Match:

.. code:: python

    In [1]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [2]: match = re.search(r'Host (\S+) in vlan (\d+) .* port (\S+) and port (\S+)', log)

    In [3]: match
    Out[3]: <_sre.SRE_Match object; span=(47, 124), match='Host f03a.b216.7ad7 in vlan 10 is flapping betwee>'

Вывод в 3 строке просто отображает информацию об объекте. Поэтому не
стоит полагаться на то, что отображается в части match, так как
отображаемая строка обрезается по фиксированному количеству знаков.

``group``
^^^^^^^

Метод group возвращает подстроку, которая совпала с выражением или с
выражением в группе.

Если метод вызывается без аргументов, отображается вся подстрока:

.. code:: python

    In [4]: match.group()
    Out[4]: 'Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

Аналогичный вывод возвращает группа 0:

.. code:: python

    In [5]: match.group(0)
    Out[5]: 'Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

Другие номера отображают только содержимое соответствующей группы:

.. code:: python

    In [6]: match.group(1)
    Out[6]: 'f03a.b216.7ad7'

    In [7]: match.group(2)
    Out[7]: '10'

    In [8]: match.group(3)
    Out[8]: 'Gi0/5'

    In [9]: match.group(4)
    Out[9]: 'Gi0/15'

Если вызвать метод group с номером группы, который больше, чем
количество существующих групп, возникнет ошибка:

.. code:: python

    In [10]: match.group(5)
    -----------------------------------------------------------------
    IndexError                      Traceback (most recent call last)
    <ipython-input-18-9df93fa7b44b> in <module>()
    ----> 1 match.group(5)

    IndexError: no such group

Если вызвать метод с несколькими номерами групп, результатом будет
кортеж со строками, которые соответствуют совпадениям:

.. code:: python

    In [11]: match.group(1, 2, 3)
    Out[11]: ('f03a.b216.7ad7', '10', 'Gi0/5')

В группу может ничего не попасть, тогда ей будет соответствовать пустая
строка:

.. code:: python

    In [12]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [13]: match = re.search(r'Host (\S+) in vlan (\D*)', log)

    In [14]: match.group(2)
    Out[14]: ''

Если группа описывает часть шаблона и совпадений было несколько, метод
отобразит последнее совпадение:

.. code:: python

    In [15]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [16]: match = re.search(r'Host (\w{4}\.)+', log)

    In [17]: match.group(1)
    Out[17]: 'b216.'

Такой вывод получился из-за того, что выражение в скобках описывает 4
буквы или цифры, точка и после этого стоит плюс. Соответственно, сначала с
выражением в скобках совпала первая часть MAC-адреса, потом вторая. Но
запоминается и возвращается только последнее выражение.

Если в выражении использовались именованные группы, методу group можно
передать имя группы и получить соответствующую подстроку:

.. code:: python

    In [18]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [19]: match = re.search(r'Host (?P<mac>\S+) '
        ...:                   r'in vlan (?P<vlan>\d+) .* '
        ...:                   r'port (?P<int1>\S+) '
        ...:                   r'and port (?P<int2>\S+)',
        ...:                   log)
        ...:

    In [20]: match.group('mac')
    Out[20]: 'f03a.b216.7ad7'

    In [21]: match.group('int2')
    Out[21]: 'Gi0/15'

При этом группы доступны и по номеру:

.. code:: python

    In [22]: match.group(3)
    Out[22]: 'Gi0/5'

    In [23]: match.group(4)
    Out[23]: 'Gi0/15'

``groups``
^^^^^^^^

Метод ``groups`` возвращает кортеж со строками, в котором элементы - это
те подстроки, которые попали в соответствующие группы:

.. code:: python

    In [24]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [25]: match = re.search(r'Host (\S+) '
        ...:                   r'in vlan (\d+) .* '
        ...:                   r'port (\S+) '
        ...:                   r'and port (\S+)',
        ...:                   log)
        ...:

    In [26]: match.groups()
    Out[26]: ('f03a.b216.7ad7', '10', 'Gi0/5', 'Gi0/15')

У метода groups есть опциональный параметр - default. Он срабатывает в
ситуации, когда все, что попадает в группу, опционально.

Например, при такой строке, совпадение будет и в первой группе, и во
второй:

.. code:: python

    In [26]: line = '100     aab1.a1a1.a5d3    FastEthernet0/1'

    In [27]: match = re.search(r'(\d+) +(\w+)?', line)

    In [28]: match.groups()
    Out[28]: ('100', 'aab1')

Если же в строке нет ничего после пробела, в группу ничего не попадет.
Но совпадение будет, так как в регулярном выражении описано, что группа
опциональна:

.. code:: python

    In [30]: line = '100     '

    In [31]: match = re.search(r'(\d+) +(\w+)?', line)

    In [32]: match.groups()
    Out[32]: ('100', None)

Соответственно, для второй группы значением будет None.

Если передать методу groups значение default, оно будет возвращаться вместо None:

.. code:: python

    In [33]: line = '100     '

    In [34]: match = re.search(r'(\d+) +(\w+)?', line)

    In [35]: match.groups(default=0)
    Out[35]: ('100', 0)

    In [36]: match.groups(default='No match')
    Out[36]: ('100', 'No match')

``groupdict``
^^^^^^^^^^^

Метод ``groupdict`` возвращает словарь, в котором ключи - имена групп, а
значения - соответствующие строки:

.. code:: python

    In [37]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [38]: match = re.search(r'Host (?P<mac>\S+) '
        ...:                   r'in vlan (?P<vlan>\d+) .* '
        ...:                   r'port (?P<int1>\S+) '
        ...:                   r'and port (?P<int2>\S+)',
        ...:                   log)
        ...:

    In [39]: match.groupdict()
    Out[39]: {'int1': 'Gi0/5', 'int2': 'Gi0/15', 'mac': 'f03a.b216.7ad7', 'vlan': '10'}

``start``, ``end``
^^^^^^^^^^^^^^

Методы ``start`` и ``end`` возвращают индексы начала и конца совпадения с
регулярным выражением.

Если методы вызываются без аргументов, они возвращают индексы для всего
совпадения:

.. code:: python

    In [40]: line = '  10     aab1.a1a1.a5d3    FastEthernet0/1  '

    In [41]: match = re.search(r'(\d+) +([0-9a-f.]+) +(\S+)', line)

    In [42]: match.start()
    Out[42]: 2

    In [43]: match.end()
    Out[43]: 42

    In [45]: line[match.start():match.end()]
    Out[45]: '10     aab1.a1a1.a5d3    FastEthernet0/1'

Методам можно передавать номер или имя группы. Тогда они возвращают
индексы для этой группы:

.. code:: python

    In [46]: match.start(2)
    Out[46]: 9

    In [47]: match.end(2)
    Out[47]: 23

    In [48]: line[match.start(2):match.end(2)]
    Out[48]: 'aab1.a1a1.a5d3'

Аналогично для именованных групп:

.. code:: python

    In [49]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [50]: match = re.search(r'Host (?P<mac>\S+) '
        ...:                   r'in vlan (?P<vlan>\d+) .* '
        ...:                   r'port (?P<int1>\S+) '
        ...:                   r'and port (?P<int2>\S+)',
        ...:                   log)
        ...:

    In [51]: match.start('mac')
    Out[51]: 52

    In [52]: match.end('mac')
    Out[52]: 66

``span``
^^^^^^

Метод ``span`` возвращает кортеж с индексом начала и конца подстроки. Он
работает аналогично методам ``start``, ``end``, но возвращает пару чисел.

Без аргументов метод span возвращает индексы для всего совпадения:

.. code:: python

    In [53]: line = '  10     aab1.a1a1.a5d3    FastEthernet0/1  '

    In [54]: match = re.search(r'(\d+) +([0-9a-f.]+) +(\S+)', line)

    In [55]: match.span()
    Out[55]: (2, 42)

Но ему также можно передать номер группы:

.. code:: python

    In [56]: line = '  10     aab1.a1a1.a5d3    FastEthernet0/1  '

    In [57]: match = re.search(r'(\d+) +([0-9a-f.]+) +(\S+)', line)

    In [58]: match.span(2)
    Out[58]: (9, 23)

Аналогично для именованных групп:

.. code:: python

    In [59]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [60]: match = re.search(r'Host (?P<mac>\S+) '
        ...:                   r'in vlan (?P<vlan>\d+) .* '
        ...:                   r'port (?P<int1>\S+) '
        ...:                   r'and port (?P<int2>\S+)',
        ...:                   log)
        ...:

    In [64]: match.span('mac')
    Out[64]: (52, 66)

    In [65]: match.span('vlan')
    Out[65]: (75, 77)

