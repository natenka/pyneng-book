Примеры использования TextFSM
-----------------------------

В этом разделе рассматриваются примеры шаблонов и использования TextFSM.

Для обработки вывода команд по шаблону в разделе используется скрипт
parse_output.py. Он не привязан к конкретному шаблону и выводу: шаблон
и вывод команды будут передаваться как аргументы:

.. code:: python

    import sys
    import textfsm
    from tabulate import tabulate

    template = sys.argv[1]
    output_file = sys.argv[2]

    with open(template) as f, open(output_file) as output:
        re_table = textfsm.TextFSM(f)
        header = re_table.header
        result = re_table.ParseText(output.read())
        print(result)
        print(tabulate(result, headers=header))


Пример запуска скрипта:

::

    $ python parse_output.py template command_output

.. note::

    Модуль tabulate используется для отображения данных в табличном виде
    (его нужно установить, если хотите использовать этот скрипт).
    Аналогичный вывод можно было сделать и с помощью форматирования
    строк, но с tabulate это сделать проще.

Обработка данных по шаблону всегда выполняется одинаково. Поэтому скрипт
будет одинаковый, только шаблон и данные будут отличаться.

Начиная с простого примера, разберемся с тем, как использовать TextFSM.

show clock
~~~~~~~~~~

Первый пример - разбор вывода команды sh clock (файл
output/sh_clock.txt):

::

    15:10:44.867 UTC Sun Nov 13 2016

Для начала в шаблоне надо определить переменные:

* в начале каждой строки должно быть ключевое слово Value
* каждая переменная определяет столбец в таблице
* следующее слово - название переменной
* после названия, в скобках - регулярное выражение, которое описывает значение переменной

Определение переменных выглядит так:

::

    Value Time (..:..:..)
    Value Timezone (\S+)
    Value WeekDay (\w+)
    Value Month (\w+)
    Value MonthDay (\d+)
    Value Year (\d+)

Подсказка по спецсимволам: 

* ``.`` - любой символ 
* ``+`` - одно или более повторений предыдущего символа 
* ``\S`` - все символы, кроме whitespace 
* ``\w`` - любая буква или цифра 
* ``\d`` - любая цифра

После определения переменных должна идти пустая строка и состояние
**Start**, а после, начиная с пробела и символа ``^``, идет правило
(файл templates/sh_clock.template):

::

    Value Time (..:..:..)
    Value Timezone (\S+)
    Value WeekDay (\w+)
    Value Month (\w+)
    Value MonthDay (\d+)
    Value Year (\d+)

    Start
      ^${Time}.* ${Timezone} ${WeekDay} ${Month} ${MonthDay} ${Year} -> Record

Так как в данном случае в выводе всего одна строка, можно не писать
в шаблоне действие Record. Но лучше его использовать в ситуациях,
когда надо записать значения, чтобы привыкать к этому синтаксису и
не ошибиться, когда нужна обработка нескольких строк.

Когда TextFSM обрабатывает строки вывода, он подставляет вместо
переменных их значения. В итоге правило будет выглядеть так:

::

    ^(..:..:..).* (\S+) (\w+) (\w+) (\d+) (\d+)

Когда это регулярное выражение применяется к выводу show clock, в каждой
группе регулярного выражения будет находиться соответствующее значение:

* 1 группа: 15:10:44 
* 2 группа: UTC 
* 3 группа: Sun 
* 4 группа: Nov
* 5 группа: 13 
* 6 группа: 2016

В правиле, кроме явного действия Record, которое указывает, что запись
надо поместить в финальную таблицу, по умолчанию также используется
правило Next. Оно указывает, что надо перейти к следующей строке текста.
Так как в выводе команды sh clock только одна строка, обработка
завершается.

Результат отработки скрипта будет таким:

::

    $ python parse_output.py templates/sh_clock.template output/sh_clock.txt
    Time      Timezone    WeekDay    Month      MonthDay    Year
    --------  ----------  ---------  -------  ----------  ------
    15:10:44  UTC         Sun        Nov              13    2016


show ip interface brief
~~~~~~~~~~~~~~~~~~~~~~~

В случае, когда нужно обработать данные, которые выведены столбцами,
шаблон TextFSM наиболее удобен.

Шаблон для вывода команды show ip interface brief (файл
templates/sh_ip_int_br.template):

::

    Value INTF (\S+)
    Value ADDR (\S+)
    Value STATUS (up|down|administratively down)
    Value PROTO (up|down)

    Start
      ^${INTF}\s+${ADDR}\s+\w+\s+\w+\s+${STATUS}\s+${PROTO} -> Record

В этом случае правило можно описать одной строкой.

Вывод команды (файл output/sh_ip_int_br.txt):

::

    R1#show ip interface brief
    Interface                  IP-Address      OK? Method Status                Protocol
    FastEthernet0/0            15.0.15.1       YES manual up                    up
    FastEthernet0/1            10.0.12.1       YES manual up                    up
    FastEthernet0/2            10.0.13.1       YES manual up                    up
    FastEthernet0/3            unassigned      YES unset  up                    up
    Loopback0                  10.1.1.1        YES manual up                    up
    Loopback100                100.0.0.1       YES manual up                    up

Результат выполнения будет таким:

::

    $ python parse_output.py templates/sh_ip_int_br.template output/sh_ip_int_br.txt
    INT              ADDR        STATUS    PROTO
    ---------------  ----------  --------  -------
    FastEthernet0/0  15.0.15.1   up        up
    FastEthernet0/1  10.0.12.1   up        up
    FastEthernet0/2  10.0.13.1   up        up
    FastEthernet0/3  unassigned  up        up
    Loopback0        10.1.1.1    up        up
    Loopback100      100.0.0.1   up        up

show cdp neighbors detail
~~~~~~~~~~~~~~~~~~~~~~~~~

Теперь попробуем обработать вывод команды show cdp neighbors detail.

Особенность этой команды в том, что данные находятся не в одной строке,
а в разных.

В файле output/sh_cdp_n_det.txt находится вывод команды show cdp
neighbors detail:

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
    Copyright (c) 1986-2009 by Cisco Systems, Inc.
    Compiled Fri 19-Jun-09 18:40 by prod_rel_team

    advertisement version: 2
    VTP Management Domain: ''
    Duplex: full
    Management address(es):

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
    Copyright (c) 1986-2009 by Cisco Systems, Inc.
    Compiled Fri 19-Jun-09 18:40 by prod_rel_team

    advertisement version: 2
    VTP Management Domain: ''
    Duplex: full
    Management address(es):

Из вывода команды надо получить такие поля: 

* LOCAL_HOST - имя устройства из приглашения 
* DEST_HOST - имя соседа 
* MGMNT_IP - IP-адрес соседа 
* PLATFORM - модель соседнего устройства 
* LOCAL_PORT - локальный интерфейс, который соединен с соседом 
* REMOTE_PORT - порт соседнего устройства 
* IOS_VERSION - версия IOS соседа

Шаблон выглядит таким образом (файл templates/sh_cdp_n_det.template):

::

    Value LOCAL_HOST (\S+)
    Value DEST_HOST (\S+)
    Value MGMNT_IP (.*)
    Value PLATFORM (.*)
    Value LOCAL_PORT (.*)
    Value REMOTE_PORT (.*)
    Value IOS_VERSION (\S+)

    Start
      ^${LOCAL_HOST}[>#].
      ^Device ID: ${DEST_HOST}
      ^.*IP address: ${MGMNT_IP}
      ^Platform: ${PLATFORM},
      ^Interface: ${LOCAL_PORT},  Port ID \(outgoing port\): ${REMOTE_PORT}
      ^.*Version ${IOS_VERSION},

Результат выполнения скрипта:

::

    $ python parse_output.py templates/sh_cdp_n_det.template output/sh_cdp_n_det.txt
    LOCAL_HOST    DEST_HOST    MGMNT_IP    PLATFORM    LOCAL_PORT             REMOTE_PORT         IOS_VERSION
    ------------  -----------  ----------  ----------  ---------------------  ------------------  -------------
    SW1           R2           10.2.2.2    Cisco 2911  GigabitEthernet1/0/21  GigabitEthernet0/0  15.2(2)T1

Несмотря на то, что правила с переменными описаны в разных строках, и,
соответственно, работают с разными строками, TextFSM собирает их в одну
строку таблицы. То есть, переменные, которые определены в начале
шаблона, задают строку итоговой таблицы.

Обратите внимание, что в файле sh_cdp_n_det.txt находится вывод с
тремя соседями, а в таблице только один сосед, последний.

Record
^^^^^^

Так получилось из-за того, что в шаблоне не указано действие **Record**.
И в итоге в финальной таблице осталась только последняя строка.

Исправленный шаблон:

::

    Value LOCAL_HOST (\S+)
    Value DEST_HOST (\S+)
    Value MGMNT_IP (.*)
    Value PLATFORM (.*)
    Value LOCAL_PORT (.*)
    Value REMOTE_PORT (.*)
    Value IOS_VERSION (\S+)

    Start
      ^${LOCAL_HOST}[>#].
      ^Device ID: ${DEST_HOST}
      ^.*IP address: ${MGMNT_IP}
      ^Platform: ${PLATFORM},
      ^Interface: ${LOCAL_PORT},  Port ID \(outgoing port\): ${REMOTE_PORT}
      ^.*Version ${IOS_VERSION}, -> Record

Теперь результат запуска скрипта выглядит так:

::

    $ python parse_output.py templates/sh_cdp_n_det.template output/sh_cdp_n_det.txt
    LOCAL_HOST    DEST_HOST    MGMNT_IP    PLATFORM              LOCAL_PORT             REMOTE_PORT         IOS_VERSION
    ------------  -----------  ----------  --------------------  ---------------------  ------------------  -------------
    SW1           SW2          10.1.1.2    cisco WS-C2960-8TC-L  GigabitEthernet1/0/16  GigabitEthernet0/1  12.2(55)SE9
                  R1           10.1.1.1    Cisco 3825            GigabitEthernet1/0/22  GigabitEthernet0/0  12.4(24)T1
                  R2           10.2.2.2    Cisco 2911            GigabitEthernet1/0/21  GigabitEthernet0/0  15.2(2)T1

Вывод получен со всех трёх устройств. Но переменная LOCAL_HOST
отображается не в каждой строке, а только в первой.

Filldown
^^^^^^^^

Это связано с тем, что приглашение, из которого взято значение
переменной, появляется только один раз. И для того, чтобы оно появлялось
и в последующих строках, надо использовать действие **Filldown** для
переменной LOCAL_HOST:

::

    Value Filldown LOCAL_HOST (\S+)
    Value DEST_HOST (\S+)
    Value MGMNT_IP (.*)
    Value PLATFORM (.*)
    Value LOCAL_PORT (.*)
    Value REMOTE_PORT (.*)
    Value IOS_VERSION (\S+)

    Start
      ^${LOCAL_HOST}[>#].
      ^Device ID: ${DEST_HOST}
      ^.*IP address: ${MGMNT_IP}
      ^Platform: ${PLATFORM},
      ^Interface: ${LOCAL_PORT},  Port ID \(outgoing port\): ${REMOTE_PORT}
      ^.*Version ${IOS_VERSION}, -> Record

Теперь мы получили такой вывод:

::

    $ python parse_output.py templates/sh_cdp_n_det.template output/sh_cdp_n_det.txt
    LOCAL_HOST    DEST_HOST    MGMNT_IP    PLATFORM              LOCAL_PORT             REMOTE_PORT         IOS_VERSION
    ------------  -----------  ----------  --------------------  ---------------------  ------------------  -------------
    SW1           SW2          10.1.1.2    cisco WS-C2960-8TC-L  GigabitEthernet1/0/16  GigabitEthernet0/1  12.2(55)SE9
    SW1           R1           10.1.1.1    Cisco 3825            GigabitEthernet1/0/22  GigabitEthernet0/0  12.4(24)T1
    SW1           R2           10.2.2.2    Cisco 2911            GigabitEthernet1/0/21  GigabitEthernet0/0  15.2(2)T1
    SW1

Теперь значение переменной LOCAL_HOST появилось во всех трёх строках.
Но появился ещё один странный эффект - последняя строка, в которой
заполнена только колонка LOCAL_HOST.

Required
^^^^^^^^

Дело в том, что все переменные, которые мы определили, опциональны. К
тому же, одна переменная с параметром Filldown. И, чтобы избавиться от
последней строки, нужно сделать хотя бы одну переменную обязательной с
помощью параметра **Required**:

::

    Value Filldown LOCAL_HOST (\S+)
    Value Required DEST_HOST (\S+)
    Value MGMNT_IP (.*)
    Value PLATFORM (.*)
    Value LOCAL_PORT (.*)
    Value REMOTE_PORT (.*)
    Value IOS_VERSION (\S+)

    Start
      ^${LOCAL_HOST}[>#].
      ^Device ID: ${DEST_HOST}
      ^.*IP address: ${MGMNT_IP}
      ^Platform: ${PLATFORM},
      ^Interface: ${LOCAL_PORT},  Port ID \(outgoing port\): ${REMOTE_PORT}
      ^.*Version ${IOS_VERSION}, -> Record

Теперь мы получим корректный вывод:

::

    $ python parse_output.py templates/sh_cdp_n_det.template output/sh_cdp_n_det.txt
    LOCAL_HOST    DEST_HOST    MGMNT_IP    PLATFORM              LOCAL_PORT             REMOTE_PORT         IOS_VERSION
    ------------  -----------  ----------  --------------------  ---------------------  ------------------  -------------
    SW1           SW2          10.1.1.2    cisco WS-C2960-8TC-L  GigabitEthernet1/0/16  GigabitEthernet0/1  12.2(55)SE9
    SW1           R1           10.1.1.1    Cisco 3825            GigabitEthernet1/0/22  GigabitEthernet0/0  12.4(24)T1
    SW1           R2           10.2.2.2    Cisco 2911            GigabitEthernet1/0/21  GigabitEthernet0/0  15.2(2)T1


show ip route ospf
~~~~~~~~~~~~~~~~~~

Рассмотрим случай, когда нам нужно обработать вывод команды show ip
route ospf, и в таблице маршрутизации есть несколько маршрутов к одной
сети.

Для маршрутов к одной и той же сети вместо нескольких строк, где будет
повторяться сеть, будет создана одна запись, в которой все доступные
next-hop адреса собраны в список.

Пример вывода команды show ip route ospf (файл
output/sh_ip_route_ospf.txt):

::

    R1#sh ip route ospf
    Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
           D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
           N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
           E1 - OSPF external type 1, E2 - OSPF external type 2
           i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
           ia - IS-IS inter area, * - candidate default, U - per-user static route
           o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
           + - replicated route, % - next hop override

    Gateway of last resort is not set

          10.0.0.0/8 is variably subnetted, 10 subnets, 2 masks
    O        10.1.1.0/24 [110/20] via 10.0.12.2, 1w2d, Ethernet0/1
    O        10.2.2.0/24 [110/20] via 10.0.13.3, 1w2d, Ethernet0/2
    O        10.3.3.3/32 [110/11] via 10.0.12.2, 1w2d, Ethernet0/1
    O        10.4.4.4/32 [110/11] via 10.0.13.3, 1w2d, Ethernet0/2
                         [110/11] via 10.0.14.4, 1w2d, Ethernet0/3
    O        10.5.5.5/32 [110/21] via 10.0.13.3, 1w2d, Ethernet0/2
                         [110/21] via 10.0.12.2, 1w2d, Ethernet0/1
                         [110/21] via 10.0.14.4, 1w2d, Ethernet0/3
    O        10.6.6.0/24 [110/20] via 10.0.13.3, 1w2d, Ethernet0/2


Для этого примера упрощаем задачу и считаем, что маршруты могут быть
только OSPF и с обозначением только O (то есть, только
внутризональные маршруты).

Первая версия шаблона выглядит так:

::

    Value network (\S+)
    Value mask (\d+)
    Value distance (\d+)
    Value metric (\d+)
    Value nexthop (\S+)

    Start
      ^O +${network}/${mask}\s\[${distance}/${metric}\]\svia\s${nexthop}, -> Record


Результат получился такой:

::

    network      mask    distance    metric  nexthop
    ---------  ------  ----------  --------  ---------
    10.1.1.0       24         110        20  10.0.12.2
    10.2.2.0       24         110        20  10.0.13.3
    10.3.3.3       32         110        11  10.0.12.2
    10.4.4.4       32         110        11  10.0.13.3
    10.5.5.5       32         110        21  10.0.13.3
    10.6.6.0       24         110        20  10.0.13.3


Всё нормально, но потерялись варианты путей для маршрутов 10.4.4.4/32 и 10.5.5.5/32.
Это логично, ведь нет правила, которое подошло бы для такой строки.


Добавляем в шаблон правило для строк с частичными записями:

::

    Value network (\S+)
    Value mask (\d+)
    Value distance (\d+)
    Value metric (\d+)
    Value nexthop (\S+)

    Start
      ^O +${network}/${mask}\s\[${distance}/${metric}\]\svia\s${nexthop}, -> Record
      ^\s+\[${distance}/${metric}\]\svia\s${nexthop}, -> Record

Теперь вывод выглядит так:

::

    network    mask      distance    metric  nexthop
    ---------  ------  ----------  --------  ---------
    10.1.1.0   24             110        20  10.0.12.2
    10.2.2.0   24             110        20  10.0.13.3
    10.3.3.3   32             110        11  10.0.12.2
    10.4.4.4   32             110        11  10.0.13.3
                              110        11  10.0.14.4
    10.5.5.5   32             110        21  10.0.13.3
                              110        21  10.0.12.2
                              110        21  10.0.14.4
    10.6.6.0   24             110        20  10.0.13.3


В частичных записях нехватает сети и маски, но в предыдущих примерах мы уже рассматривали Filldown и, при желании, его можно применить тут, но для этого примера будет использоваться другая опция - List.


List
^^^^

Воспользуемся опцией **List** для переменной nexthop:

::

    Value network (\S+)
    Value mask (\d+)
    Value distance (\d+)
    Value metric (\d+)
    Value List nexthop (\S+)

    Start
      ^O +${network}/${mask}\s\[${distance}/${metric}\]\svia\s${nexthop}, -> Record
      ^\s+\[${distance}/${metric}\]\svia\s${nexthop}, -> Record


Теперь вывод получился таким:

::

    network    mask      distance    metric  nexthop
    ---------  ------  ----------  --------  -------------
    10.1.1.0   24             110        20  ['10.0.12.2']
    10.2.2.0   24             110        20  ['10.0.13.3']
    10.3.3.3   32             110        11  ['10.0.12.2']
    10.4.4.4   32             110        11  ['10.0.13.3']
                              110        11  ['10.0.14.4']
    10.5.5.5   32             110        21  ['10.0.13.3']
                              110        21  ['10.0.12.2']
                              110        21  ['10.0.14.4']
    10.6.6.0   24             110        20  ['10.0.13.3']



Изменилось то, что в столбце nexthop отображается список, но пока с
одним элементом. При использовании List - значение это список, и каждое совпадение с регулярным выражением будет добавлять в список элемент. По умолчанию каждое следующее совпадение перезаписывает предыдущее.
Если, например, оставить действие Record только для полных строк:

::

    Value network (\S+)
    Value mask (\d+)
    Value distance (\d+)
    Value metric (\d+)
    Value List nexthop (\S+)

    Start
      ^O +${network}/${mask}\s\[${distance}/${metric}\]\svia\s${nexthop}, -> Record
      ^\s+\[${distance}/${metric}\]\svia\s${nexthop},

Результат будет таким:

::

    network      mask    distance    metric  nexthop
    ---------  ------  ----------  --------  ---------------------------------------
    10.1.1.0       24         110        20  ['10.0.12.2']
    10.2.2.0       24         110        20  ['10.0.13.3']
    10.3.3.3       32         110        11  ['10.0.12.2']
    10.4.4.4       32         110        11  ['10.0.13.3']
    10.5.5.5       32         110        21  ['10.0.14.4', '10.0.13.3']
    10.6.6.0       24         110        20  ['10.0.12.2', '10.0.14.4', '10.0.13.3']

Сейчас результат не совсем правильный, адреса хопов записались не к тем маршрутам.
Так получилось потому что запись выполняется на полном маршруте, затем хопы 
неполных маршрутов собираются в список (другие переменные перезаписываются)
и когда встречается следующий полный маршрут, список записывается к нему.

::

    O        10.4.4.4/32 [110/11] via 10.0.13.3, 1w2d, Ethernet0/2
                         [110/11] via 10.0.14.4, 1w2d, Ethernet0/3
    O        10.5.5.5/32 [110/21] via 10.0.13.3, 1w2d, Ethernet0/2
                         [110/21] via 10.0.12.2, 1w2d, Ethernet0/1
                         [110/21] via 10.0.14.4, 1w2d, Ethernet0/3
    O        10.6.6.0/24 [110/20] via 10.0.13.3, 1w2d, Ethernet0/2


Фактически неполные маршруты действительно надо записывать, когда встретился следующий полный,
но при этом, надо записать их к соответствующему маршруту.
Надо сделать следующее: как только встретился полный маршрут, надо записать 
предыдущие значения, а затем продолжить обрабатывать тот же полный маршрут
для получения его информации. В TextFSM это можно сделать с помощью действий Continue.Record:

::

      ^O -> Continue.Record

В ней действие **Record** говорит, что надо записать текущее значение
переменных. Так как в этом правиле нет переменных, записывается то,
что было в предыдущих значениях.

Действие **Continue** говорит, что надо продолжить работать с текущей
строкой так, как будто совпадения не было. За счет этого сработает
следующая строка шаблона. Итоговый шаблон выглядит так (файл
templates/sh_ip_route_ospf.template):

::

    Value network (\S+)
    Value mask (\d+)
    Value distance (\d+)
    Value metric (\d+)
    Value List nexthop (\S+)

    Start
      ^O -> Continue.Record
      ^O +${network}/${mask}\s\[${distance}/${metric}\]\svia\s${nexthop},
      ^\s+\[${distance}/${metric}\]\svia\s${nexthop},


В результате мы получим такой вывод:

::

    network      mask    distance    metric  nexthop
    ---------  ------  ----------  --------  ---------------------------------------
    10.1.1.0       24         110        20  ['10.0.12.2']
    10.2.2.0       24         110        20  ['10.0.13.3']
    10.3.3.3       32         110        11  ['10.0.12.2']
    10.4.4.4       32         110        11  ['10.0.13.3', '10.0.14.4']
    10.5.5.5       32         110        21  ['10.0.13.3', '10.0.12.2', '10.0.14.4']
    10.6.6.0       24         110        20  ['10.0.13.3']


show etherchannel summary
~~~~~~~~~~~~~~~~~~~~~~~~~

TextFSM удобно использовать для разбора вывода, который отображается
столбцами, или для обработки вывода, который находится в разных строках.
Менее удобными получаются шаблоны, когда надо получить несколько
однотипных элементов из одной строки.

Пример вывода команды show etherchannel summary (файл
output/sh_etherchannel_summary.txt):

::

    sw1# sh etherchannel summary
    Flags:  D - down        P - bundled in port-channel
            I - stand-alone s - suspended
            H - Hot-standby (LACP only)
            R - Layer3      S - Layer2
            U - in use      f - failed to allocate aggregator

            M - not in use, minimum links not met
            u - unsuitable for bundling
            w - waiting to be aggregated
            d - default port


    Number of channel-groups in use: 2
    Number of aggregators:           2

    Group  Port-channel  Protocol    Ports
    ------+-------------+-----------+-----------------------------------------------
    1      Po1(SU)         LACP      Fa0/1(P)   Fa0/2(P)   Fa0/3(P)
    3      Po3(SU)          -        Fa0/11(P)   Fa0/12(P)   Fa0/13(P)   Fa0/14(P)

В данном случае нужно получить: 

* имя и номер port-channel. Например, Po1 
* список всех портов в нём. Например, ['Fa0/1', 'Fa0/2', 'Fa0/3']

Сложность тут в том, что порты находятся в одной строке, а в TextFSM
нельзя указывать одну и ту же переменную несколько раз в строке. Но есть
возможность несколько раз искать совпадение в строке.

Первая версия шаблона выглядит так:

::

    Value CHANNEL (\S+)
    Value List MEMBERS (\w+\d+\/\d+)

    Start
      ^\d+ +${CHANNEL}\(\S+ +[\w-]+ +[\w ]+ +${MEMBERS}\( -> Record

В шаблоне две переменные: 

* CHANNEL - имя и номер агрегированного порта
* MEMBERS - список портов, которые входят в агрегированный порт. 
  Для этой переменной указан тип - List

Результат:

::

    CHANNEL    MEMBERS
    ---------  ----------
    Po1        ['Fa0/1']
    Po3        ['Fa0/11']

Пока что в выводе только первый порт, а нужно, чтобы попали все порты. В
данном случае надо продолжить обработку строки с портами после
найденного совпадения. То есть, использовать действие Continue и описать
следующее выражение.

Единственная строка, которая есть в шаблоне, описывает первый порт. Надо
добавить строку, которая описывает следующий порт.

Следующая версия шаблона:

::

    Value CHANNEL (\S+)
    Value List MEMBERS (\w+\d+\/\d+)

    Start
      ^\d+ +${CHANNEL}\(\S+ +[\w-]+ +[\w ]+ +${MEMBERS}\( -> Continue
      ^\d+ +${CHANNEL}\(\S+ +[\w-]+ +[\w ]+ +\S+ +${MEMBERS}\( -> Record

Вторая строка описывает такое же выражение, но переменная MEMBERS
смещается на следующий порт.

Результат:

::

    CHANNEL    MEMBERS
    ---------  --------------------
    Po1        ['Fa0/1', 'Fa0/2']
    Po3        ['Fa0/11', 'Fa0/12']

Аналогично надо дописать в шаблон строки, которые описывают третий и
четвертый порт. Но, так как в выводе может быть переменное количество
портов, надо перенести правило Record на отдельную строку, чтобы оно не
было привязано к конкретному количеству портов в строке.

Если Record будет находиться, например, после строки, в которой
описаны четыре порта, для ситуации, когда портов в строке меньше,
запись не будет выполняться.

Итоговый шаблон (файл templates/sh_etherchannel_summary.txt):

::

    Value CHANNEL (\S+)
    Value List MEMBERS (\w+\d+\/\d+)

    Start
      ^\d+.* -> Continue.Record
      ^\d+ +${CHANNEL}\(\S+ +[\w-]+ +[\w ]+ +\S+ +${MEMBERS}\( -> Continue
      ^\d+ +${CHANNEL}\(\S+ +[\w-]+ +[\w ]+ +(\S+ +){2} +${MEMBERS}\( -> Continue
      ^\d+ +${CHANNEL}\(\S+ +[\w-]+ +[\w ]+ +(\S+ +){3} +${MEMBERS}\( -> Continue

Результат обработки:

::

    CHANNEL    MEMBERS
    ---------  ----------------------------------------
    Po1        ['Fa0/1', 'Fa0/2', 'Fa0/3']
    Po3        ['Fa0/11', 'Fa0/12', 'Fa0/13', 'Fa0/14']

Теперь все порты попали в вывод.

    Шаблон предполагает, что в одной строке будет максимум четыре порта.
    Если портов может быть больше, надо добавить соответствующие строки
    в шаблон.

Возможен ещё один вариант вывода команды sh etherchannel summary (файл
output/sh_etherchannel_summary2.txt):

::

    sw1# sh etherchannel summary
    Flags:  D - down        P - bundled in port-channel
            I - stand-alone s - suspended
            H - Hot-standby (LACP only)
            R - Layer3      S - Layer2
            U - in use      f - failed to allocate aggregator

            M - not in use, minimum links not met
            u - unsuitable for bundling
            w - waiting to be aggregated
            d - default port


    Number of channel-groups in use: 2
    Number of aggregators:           2

    Group  Port-channel  Protocol    Ports
    ------+-------------+-----------+-----------------------------------------------
    1      Po1(SU)         LACP      Fa0/1(P)   Fa0/2(P)   Fa0/3(P)
    3      Po3(SU)          -        Fa0/11(P)   Fa0/12(P)   Fa0/13(P)   Fa0/14(P)
                                     Fa0/15(P)   Fa0/16(P)

В таком выводе появляется новый вариант - строки, в которых находятся
только порты.

Для того, чтобы шаблон обрабатывал и этот вариант, надо его
модифицировать (файл templates/sh_etherchannel_summary2.txt):

::

    Value CHANNEL (\S+)
    Value List MEMBERS (\w+\d+\/\d+)

    Start
      ^\d+.* -> Continue.Record
      ^\d+ +${CHANNEL}\(\S+ +[\w-]+ +[\w ]+ +${MEMBERS}\( -> Continue
      ^\d+ +${CHANNEL}\(\S+ +[\w-]+ +[\w ]+ +\S+ +${MEMBERS}\( -> Continue
      ^\d+ +${CHANNEL}\(\S+ +[\w-]+ +[\w ]+ +(\S+ +){2} +${MEMBERS}\( -> Continue
      ^\d+ +${CHANNEL}\(\S+ +[\w-]+ +[\w ]+ +(\S+ +){3} +${MEMBERS}\( -> Continue
      ^ +${MEMBERS} -> Continue
      ^ +\S+ +${MEMBERS} -> Continue
      ^ +(\S+ +){2} +${MEMBERS} -> Continue
      ^ +(\S+ +){3} +${MEMBERS} -> Continue

Результат будет таким:

::

    CHANNEL    MEMBERS
    ---------  ------------------------------------------------------------
    Po1        ['Fa0/1', 'Fa0/2', 'Fa0/3']
    Po3        ['Fa0/11', 'Fa0/12', 'Fa0/13', 'Fa0/14', 'Fa0/15', 'Fa0/16']

На этом мы заканчиваем разбираться с шаблонами TextFSM.

Примеры шаблонов для Cisco и другого оборудования можно посмотреть в
проекте
`ntc-ansible <https://github.com/networktocode/ntc-templates/tree/master/ntc_templates/templates>`__.

