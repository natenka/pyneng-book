Объект Match
~~~~~~~~~~~~

В модуле re несколько функций возвращают объект Match, если было найдено
совпадение: \* search \* match \* finditer возвращает итератор с
объектами Match

В этом подразделе рассматриваются методы объекта Match.

Пример объекта Match:

.. code:: python

    In [1]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [2]: match = re.search('Host (\S+) in vlan (\d+) .* port (\S+) and port (\S+)', log)

    In [3]: match
    Out[3]: <_sre.SRE_Match object; span=(47, 124), match='Host f03a.b216.7ad7 in vlan 10 is flapping betwee>

Вывод в 3 строке просто отображает информацию об объекте. Поэтому не
стоит полагаться на то, что отображается в части match, так как
отображаемая строка обрезается по фиксированному количеству знаков.

group()
^^^^^^^

Метод group возвращает подстроку, которая совпала с выражением или с
выражением в группе.

Если метод вызывается без аргументов, отображается вся подстрока:

.. code:: python

    In [4]: match.group()
    Out[4]: 'Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

На самом деле в этом случае метод group вызывается с группой 0:

.. code:: python

    In [13]: match.group(0)
    Out[13]: 'Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

Другие номера отображают только содержимое соответствующей группы:

.. code:: python

    In [14]: match.group(1)
    Out[14]: 'f03a.b216.7ad7'

    In [15]: match.group(2)
    Out[15]: '10'

    In [16]: match.group(3)
    Out[16]: 'Gi0/5'

    In [17]: match.group(4)
    Out[17]: 'Gi0/15'

Если вызвать метод group с номером группы, который больше, чем
количество существующих групп, возникнет ошибка:

.. code:: python

    In [18]: match.group(5)
    -----------------------------------------------------------------
    IndexError                      Traceback (most recent call last)
    <ipython-input-18-9df93fa7b44b> in <module>()
    ----> 1 match.group(5)

    IndexError: no such group

Если вызвать метод с несколькими номерами групп, результатом будет
кортеж со строками, которые соответствуют совпадениям:

.. code:: python

    In [19]: match.group(1, 2, 3)
    Out[19]: ('f03a.b216.7ad7', '10', 'Gi0/5')

В группу может ничего не попасть, тогда ей будет соответствовать пустая
строка:

.. code:: python

    In [1]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [34]: match = re.search('Host (\S+) in vlan (\D*)', log)

    In [36]: match.group(2)
    Out[36]: ''

Если группа описывает часть шаблона и совпадений было несколько, метод
отобразит последнее совпадение:

.. code:: python

    In [1]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [44]: match = re.search('Host (\w{4}\.)+', log)

    In [45]: match.group(1)
    Out[46]: 'b216.'

Такой вывод получился из-за того, что выражение в скобках описывает 4
буквы или цифры, и после этого стоит плюс. Соответственно, сначала с
выражением в скобках совпала первая часть MAC-адреса, потом вторая. Но
запоминается и возвращается только последнее выражение.

Если в выражении использовались именованные группы, методу group можно
передать имя группы и получить соответствующую подстроку:

.. code:: python

    In [1]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [55]: match = re.search('Host (?P<mac>\S+) '
        ...:                   'in vlan (?P<vlan>\d+) .* '
        ...:                   'port (?P<int1>\S+) '
        ...:                   'and port (?P<int2>\S+)',
        ...:                   log)
        ...:

    In [53]: match.group('mac')
    Out[53]: 'f03a.b216.7ad7'

    In [54]: match.group('int2')
    Out[54]: 'Gi0/15'

Но эти же группы доступны и по номеру:

.. code:: python

    In [58]: match.group(3)
    Out[58]: 'Gi0/5'

    In [59]: match.group(4)
    Out[59]: 'Gi0/15'

groups()
^^^^^^^^

Метод groups() возвращает кортеж со строками, в котором элементы - это
те подстроки, которые попали в соответствующие группы:

.. code:: python

    In [63]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [64]: match = re.search('Host (\S+) '
        ...:                   'in vlan (\d+) .* '
        ...:                   'port (\S+) '
        ...:                   'and port (\S+)',
        ...:                   log)
        ...:

    In [65]: match.groups()
    Out[65]: ('f03a.b216.7ad7', '10', 'Gi0/5', 'Gi0/15')

У метода groups есть опциональный параметр - default. Он срабатывает в
ситуации, когда все, что попадает в группу, опционально.

Например, при такой строке, совпадение будет и в первой группе, и во
второй:

.. code:: python

    In [76]: line = '100     aab1.a1a1.a5d3    FastEthernet0/1'

    In [77]: match = re.search('(\d+) +(\w+)?', line)

    In [78]: match.groups()
    Out[78]: ('100', 'aab1')

Если же в строке нет ничего после пробела, в группу ничего не попадет.
Но совпадение будет, так как в регулярном выражении описано, что группа
опциональна:

.. code:: python

    In [80]: line = '100     '

    In [81]: match = re.search('(\d+) +(\w+)?', line)

    In [82]: match.groups()
    Out[82]: ('100', None)

Соответственно, для второй группы значением будет None.

Но если передать методу groups аргумент, он будет возвращаться вместо
None:

.. code:: python

    In [83]: line = '100     '

    In [84]: match = re.search('(\d+) +(\w+)?', line)

    In [85]: match.groups(0)
    Out[85]: ('100', 0)

    In [86]: match.groups('No match')
    Out[86]: ('100', 'No match')

groupdict()
^^^^^^^^^^^

Метод groupdict возвращает словарь, в котором ключи - имена групп, а
значения - соответствующие строки:

.. code:: python

    In [63]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [88]: match = re.search('Host (?P<mac>\S+) '
        ...:                   'in vlan (?P<vlan>\d+) .* '
        ...:                   'port (?P<int1>\S+) '
        ...:                   'and port (?P<int2>\S+)',
        ...:                   log)
        ...:

    In [89]: match.groupdict()
    Out[89]: {'int1': 'Gi0/5', 'int2': 'Gi0/15', 'mac': 'f03a.b216.7ad7', 'vlan': '10'}

start(), end()
~~~~~~~~~~~~~~

Методы start и end возвращают индексы начала и конца совпадения с
регулярным выражением.

Если методы вызываются без аргументов, они возвращают индексы для всего
совпадения:

.. code:: python

    In [101]: line = '  10     aab1.a1a1.a5d3    FastEthernet0/1  '

    In [102]: match = re.search('(\d+) +([0-9a-f.]+) +(\S+)', line)

    In [103]: match.start()
    Out[103]: 2

    In [104]: match.end()
    Out[104]: 42

    In [105]: line[match.start():match.end()]
    Out[105]: '10     aab1.a1a1.a5d3    FastEthernet0/1'

Методам можно передавать номер или имя группы. Тогда они возвращают
индексы для этой группы:

.. code:: python

    In [108]: match.start(2)
    Out[108]: 9

    In [109]: match.end(2)
    Out[109]: 23

    In [110]: line[match.start(2):match.end(2)]
    Out[110]: 'aab1.a1a1.a5d3'

Аналогично для именованных групп:

.. code:: python

    In [63]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [88]: match = re.search('Host (?P<mac>\S+) '
        ...:                   'in vlan (?P<vlan>\d+) .* '
        ...:                   'port (?P<int1>\S+) '
        ...:                   'and port (?P<int2>\S+)',
        ...:                   log)
        ...:

    In [9]: match.start('mac')
    Out[9]: 52

    In [10]: match.end('mac')
    Out[10]: 66

span()
~~~~~~

Метод span возвращает кортеж с индексом начала и конца подстроки. Он
работает аналогично методам start, end, но возвращает пару чисел.

Без аргументов метод span возвращает индексы для всего совпадения:

.. code:: python

    In [112]: line = '  10     aab1.a1a1.a5d3    FastEthernet0/1  '

    In [113]: match = re.search('(\d+) +([0-9a-f.]+) +(\S+)', line)

    In [114]: match.span()
    Out[114]: (2, 42)

Но ему также можно передать номер группы:

.. code:: python

    In [115]: line = '  10     aab1.a1a1.a5d3    FastEthernet0/1  '

    In [116]: match = re.search('(\d+) +([0-9a-f.]+) +(\S+)', line)

    In [117]: match.span(2)
    Out[117]: (9, 23)

Аналогично для именованных групп:

.. code:: python

    In [63]: log = 'Jun  3 14:39:05.941: %SW_MATM-4-MACFLAP_NOTIF: Host f03a.b216.7ad7 in vlan 10 is flapping between port Gi0/5 and port Gi0/15'

    In [88]: match = re.search('Host (?P<mac>\S+) '
        ...:                   'in vlan (?P<vlan>\d+) .* '
        ...:                   'port (?P<int1>\S+) '
        ...:                   'and port (?P<int2>\S+)',
        ...:                   log)
        ...:

    In [14]: match.span('mac')
    Out[14]: (52, 66)

    In [15]: match.span('vlan')
    Out[15]: (75, 77)

