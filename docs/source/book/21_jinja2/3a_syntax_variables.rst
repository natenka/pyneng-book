Переменные
----------

Переменные в шаблоне указываются в двойных фигурных скобках:

::

    hostname {{ name }}

    interface Loopback0
     ip address 10.0.0.{{ id }} 255.255.255.255

Значения переменных подставляются на основе словаря, который передается
шаблону.

Переменная, которая передается в словаре, может быть не только числом
или строкой, но и, например, списком или словарем. Внутри шаблона можно,
соответственно, обращаться к элементу по номеру или по ключу.

Пример шаблона templates/variables.txt с использованием разных вариантов
переменных:

::

    hostname {{ name }}

    interface Loopback0
     ip address 10.0.0.{{ id }} 255.255.255.255

    vlan {{ vlans[0] }}

    router ospf 1
     router-id 10.0.0.{{ id }}
     auto-cost reference-bandwidth 10000
     network {{ ospf.network }} area {{ ospf['area'] }}

И соответствующий файл data_files/vars.yml с переменными:

::

    id: 3
    name: R3
    vlans:
      - 10
      - 20
      - 30
    ospf:
      network: 10.0.1.0 0.0.0.255
      area: 0

Обратите внимание на использование переменной vlans в шаблоне:  так
как переменная vlans это список, можно указывать, какой именно элемент
из списка нам нужен

Если передается словарь (как в случае с переменной ospf), то внутри
шаблона можно обращаться к объектам словаря, используя один из
вариантов: ``ospf.network или ospf['network']``

Результат запуска скрипта будет таким:

::

    $ python cfg_gen.py templates/variables.txt data_files/vars.yml
    hostname R3

    interface Loopback0
     ip address 10.0.0.3 255.255.255.255

    vlan 10

    router ospf 1
     router-id 10.0.0.3
     auto-cost reference-bandwidth 10000
     network 10.0.1.0 0.0.0.255 area 0

