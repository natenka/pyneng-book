Фильтры
-------

В Jinja переменные можно изменять с помощью фильтров. Фильтры отделяются
от переменной вертикальной чертой (pipe ``|``) и могут содержать
дополнительные аргументы.

Кроме того, к переменной могут быть применены несколько фильтров. В
таком случае фильтры просто пишутся последовательно, и каждый из них
отделен вертикальной чертой.

Jinja поддерживает большое количество встроенных фильтров. Мы рассмотрим
лишь несколько из них. Остальные фильтры можно найти в
`документации <http://jinja.pocoo.org/docs/dev/templates/#builtin-filters>`__.

Также достаточно легко можно создавать и свои собственные фильтры. Мы не
будем рассматривать эту возможность, но это хорошо описано в
`документации <http://jinja.pocoo.org/docs/2.9/api/#custom-filters>`__.

default
~~~~~~~

Фильтр default позволяет указать для переменной значение по умолчанию.
Если переменная определена, будет выводиться переменная, если переменная
не определена, будет выводиться значение, которое указано в фильтре
default.

Пример шаблона templates/filter_default.txt:

::

    router ospf 1
     auto-cost reference-bandwidth {{ ref_bw | default(10000) }}
     {% for networks in ospf %}
     network {{ networks.network }} area {{ networks.area }}
     {% endfor %}

Если переменная ref_bw определена в словаре, будет подставлено её
значение. Если же переменной нет, будет подставлено значение 10000.

Файл с данными (data_files/filter_default.yml):

.. code:: yaml

    ospf:
      - network: 10.0.1.0 0.0.0.255
        area: 0
      - network: 10.0.2.0 0.0.0.255
        area: 2
      - network: 10.1.1.0 0.0.0.255
        area: 0

Результат выполнения:

::

    $ python cfg_gen.py templates/filter_default.txt data_files/filter_default.yml
    router ospf 1
     auto-cost reference-bandwidth 10000
     network 10.0.1.0 0.0.0.255 area 0
     network 10.0.2.0 0.0.0.255 area 2
     network 10.1.1.0 0.0.0.255 area 0

По умолчанию, если переменная определена и её значение пустой объект,
будет считаться, что переменная и её значение есть.

Если нужно сделать так, чтобы значение по умолчанию подставлялось и в
том случае, когда переменная пустая (то есть, обрабатывается как False в
Python), надо указать дополнительный параметр ``boolean=true``.

Например, если файл данных был бы таким:

::

    ref_bw: ''
    ospf:
      - network: 10.0.1.0 0.0.0.255
        area: 0
      - network: 10.0.2.0 0.0.0.255
        area: 2
      - network: 10.1.1.0 0.0.0.255
        area: 0

То в итоге сгенерировался такой результат:

::

    $ python cfg_gen.py templates/filter_default.txt data_files/filter_default.yml
    router ospf 1
     auto-cost reference-bandwidth 
     network 10.0.1.0 0.0.0.255 area 0
     network 10.0.2.0 0.0.0.255 area 2
     network 10.1.1.0 0.0.0.255 area 0

Если же при таком же файле данных изменить шаблон таким образом:

::

    router ospf 1
     auto-cost reference-bandwidth {{ ref_bw | default(10000, boolean=true) }}
    {% for networks in ospf %}
     network {{ networks.network }} area {{ networks.area }}
    {% endfor %}

.. note::
    Вместо ``default(10000, boolean=true)`` можно написать
    default(10000, true)

Результат уже будет таким (значение по умолчанию подставится):

::

    $ python cfg_gen.py templates/filter_default.txt data_files/filter_default.yml
    router ospf 1
     auto-cost reference-bandwidth 10000
     network 10.0.1.0 0.0.0.255 area 0
     network 10.0.2.0 0.0.0.255 area 2
     network 10.1.1.0 0.0.0.255 area 0

dictsort
~~~~~~~~

Фильтр dictsort позволяет сортировать словарь. По умолчанию сортировка
выполняется по ключам, но, изменив параметры фильтра, можно выполнять
сортировку по значениям.

Синтаксис фильтра:

::

    dictsort(value, case_sensitive=False, by='key')

После того, как dictsort отсортировал словарь, он возвращает список
кортежей, а не словарь.

Пример шаблона templates/filter_dictsort.txt с использованием фильтра
dictsort:

::

    {% for intf, params in trunks | dictsort %}
    interface {{ intf }}
     {% if params.action == 'add' %}
     switchport trunk allowed vlan add {{ params.vlans }}
     {% elif params.action == 'delete' %}
     switchport trunk allowed vlan remove {{ params.vlans }}
     {% else %}
     switchport trunk allowed vlan {{ params.vlans }}
     {% endif %}
    {% endfor %}

Обратите внимание, что фильтр ожидает словарь, а не список кортежей
или итератор.

Файл с данными (data_files/filter_dictsort.yml):

.. code:: yaml

    trunks:
      Fa0/2:
        action: only
        vlans: 10,30
      Fa0/3:
        action: delete
        vlans: 10
      Fa0/1:
        action: add
        vlans: 10,20

Результат выполнения будет таким (интерфейсы упорядочены):

::

    $ python cfg_gen.py templates/filter_dictsort.txt data_files/filter_dictsort.yml
    interface Fa0/1
     switchport trunk allowed vlan add 10,20
    interface Fa0/2
     switchport trunk allowed vlan 10,30
    interface Fa0/3
     switchport trunk allowed vlan remove 10

join
~~~~

Фильтр join работает так же, как и метод join в Python.

С помощью фильтра join можно объединять элементы последовательности в
строку с опциональным разделителем между элементами.

Пример шаблона templates/filter_join.txt с использованием фильтра join:

::

    {% for intf, params in trunks | dictsort %}
    interface {{ intf }}
     {% if params.action == 'add' %}
     switchport trunk allowed vlan add {{ params.vlans | join(',') }}
     {% elif params.action == 'delete' %}
     switchport trunk allowed vlan remove {{ params.vlans | join(',') }}
     {% else %}
     switchport trunk allowed vlan {{ params.vlans | join(',') }}
     {% endif %}
    {% endfor %}

Файл с данными (data_files/filter_join.yml):

.. code:: yaml

    trunks:
      Fa0/1:
        action: add
        vlans:
          - 10
          - 20
      Fa0/2:
        action: only
        vlans:
          - 10
          - 30
      Fa0/3:
        action: delete
        vlans:
          - 10

Результат выполнения:

::

    $ python cfg_gen.py templates/filter_join.txt data_files/filter_join.yml
    interface Fa0/1
     switchport trunk allowed vlan add 10,20
    interface Fa0/2
     switchport trunk allowed vlan 10,30
    interface Fa0/3
     switchport trunk allowed vlan remove 10

