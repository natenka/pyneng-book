if/elif/else
------------

if позволяет добавлять условие в шаблон. Например, можно использовать
if, чтобы добавлять какие-то части шаблона в зависимости от наличия
переменных в словаре с данными.

Конструкция if также должна находиться внутри ``{% %}``.
Нужно явно указывать окончание условия:

::

    {% if ospf %}
    router ospf 1
     router-id 10.0.0.{{ id }}
     auto-cost reference-bandwidth 10000
    {% endif %}

Пример шаблона templates/if.txt:

::

    hostname {{ name }}

    interface Loopback0
     ip address 10.0.0.{{ id }} 255.255.255.255

    {% for vlan, name in vlans.items() %}
    vlan {{ vlan }}
     name {{ name }}
    {% endfor %}

    {% if ospf %}
    router ospf 1
     router-id 10.0.0.{{ id }}
     auto-cost reference-bandwidth 10000
     {% for networks in ospf %}
     network {{ networks.network }} area {{ networks.area }}
     {% endfor %}
    {% endif %}

Выражение ``if ospf`` работает так же, как в Python: если переменная
существует и не пустая, результат будет True. Если переменной нет или
она пустая, результат будет False.

То есть, в этом шаблоне конфигурация OSPF генерируется только в том
случае, если переменная ospf существует и не пустая.

Конфигурация будет генерироваться с двумя вариантами данных.

Сначала с файлом data_files/if.yml, в котором нет переменной ospf:

.. code:: yaml

    id: 3
    name: R3
    vlans:
      10: Marketing
      20: Voice
      30: Management

Результат будет таким:

::

    $ python cfg_gen.py templates/if.txt data_files/if.yml

    hostname R3

    interface Loopback0
     ip address 10.0.0.3 255.255.255.255

    vlan 10
     name Marketing
    vlan 20
     name Voice
    vlan 30
     name Management

Теперь аналогичный шаблон, но с файлом data_files/if_ospf.yml:

.. code:: yaml

    id: 3
    name: R3
    vlans:
      10: Marketing
      20: Voice
      30: Management
    ospf:
      - network: 10.0.1.0 0.0.0.255
        area: 0
      - network: 10.0.2.0 0.0.0.255
        area: 2
      - network: 10.1.1.0 0.0.0.255
        area: 0

Теперь результат выполнения будет таким:

::

    hostname R3

    interface Loopback0
     ip address 10.0.0.3 255.255.255.255

    vlan 10
     name Marketing
    vlan 20
     name Voice
    vlan 30
     name Management

    router ospf 1
     router-id 10.0.0.3
     auto-cost reference-bandwidth 10000
     network 10.0.1.0 0.0.0.255 area 0
     network 10.0.2.0 0.0.0.255 area 2
     network 10.1.1.0 0.0.0.255 area 0

Как и в Python, в Jinja можно делать ответвления в условии.

Пример шаблона templates/if_vlans.txt:

::

    {% for intf, params in trunks.items() %}
    interface {{ intf }}
     {% if params.action == 'add' %}
     switchport trunk allowed vlan add {{ params.vlans }}
     {% elif params.action == 'delete' %}
     switchport trunk allowed vlan remove {{ params.vlans }}
     {% else %}
     switchport trunk allowed vlan {{ params.vlans }}
     {% endif %}
    {% endfor %}

Файл data_files/if_vlans.yml с данными:

.. code:: yaml

    trunks:
      Fa0/1:
        action: add
        vlans: 10,20
      Fa0/2:
        action: only
        vlans: 10,30
      Fa0/3:
        action: delete
        vlans: 10

В данном примере в зависимости от значения параметра action генерируются
разные команды.

В шаблоне можно было использовать и такой вариант обращения к вложенным
словарям:

::

    {% for intf in trunks %}
    interface {{ intf }}
     {% if trunks[intf]['action'] == 'add' %}
     switchport trunk allowed vlan add {{ trunks[intf]['vlans'] }}
     {% elif trunks[intf]['action'] == 'delete' %}
     switchport trunk allowed vlan remove {{ trunks[intf]['vlans'] }}
     {% else %}
     switchport trunk allowed vlan {{ trunks[intf]['vlans'] }}
     {% endif %}
    {% endfor %}

В итоге будет сгенерирована такая конфигурация:

::

    $ python cfg_gen.py templates/if_vlans.txt data_files/if_vlans.yml
    interface Fa0/1
     switchport trunk allowed vlan add 10,20
    interface Fa0/3
     switchport trunk allowed vlan remove 10
    interface Fa0/2
     switchport trunk allowed vlan 10,30

Также с помощью if можно фильтровать, по каким элементам
последовательности пройдется цикл for.

Пример шаблона templates/if_for.txt с фильтром в цикле for:

::

    {% for vlan, name in vlans.items() if vlan > 15 %}
    vlan {{ vlan }}
     name {{ name }}
    {% endfor %}

Файл с данными (data_files/if_for.yml):

.. code:: yaml

    vlans:
      10: Marketing
      20: Voice
      30: Management

Результат выполнения:

::

    $ python cfg_gen.py templates/if_for.txt data_files/if_for.yml
    vlan 20
     name Voice
    vlan 30
     name Management

