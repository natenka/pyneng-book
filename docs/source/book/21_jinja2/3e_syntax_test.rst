Тесты
-----

Кроме фильтров, Jinja также поддерживает тесты. Тесты позволяют
проверять переменные на какое-то условие.

Jinja поддерживает большое количество встроенных тестов. Мы рассмотрим
лишь несколько из них. Остальные тесты вы можете найти в
`документации <http://jinja.pocoo.org/docs/dev/templates/#builtin-tests>`__.

Тесты, как и фильтры, можно создавать самостоятельно.

defined
~~~~~~~

Тест defined позволяет проверить, есть ли переменная в словаре данных.

Пример шаблона templates/test_defined.txt:

::

    router ospf 1
    {% if ref_bw is defined %}
     auto-cost reference-bandwidth {{ ref_bw }}
    {% else %}
     auto-cost reference-bandwidth 10000
    {% endif %}
    {% for networks in ospf %}
     network {{ networks.network }} area {{ networks.area }}
    {% endfor %}

Этот пример более громоздкий, чем вариант с использованием фильтра
default, но этот тест может быть полезен в том случае, если, в
зависимости от того, определена переменная или нет, нужно выполнять
разные команды.

Файл с данными (data_files/test_defined.yml):

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

    $ python cfg_gen.py templates/test_defined.txt data_files/test_defined.yml
    router ospf 1
     auto-cost reference-bandwidth 10000
     network 10.0.1.0 0.0.0.255 area 0
     network 10.0.2.0 0.0.0.255 area 2
     network 10.1.1.0 0.0.0.255 area 0

iterable
~~~~~~~~

Тест iterable проверяет, является ли объект итератором.

Благодаря таким проверкам, можно делать ответвления в шаблоне, которые
будут учитывать тип переменной.

Шаблон templates/test_iterable.txt (сделаны отступы, чтобы были
понятней ответвления):

::

    {% for intf, params in trunks | dictsort %}
    interface {{ intf }}
     {% if params.vlans is iterable %}
       {% if params.action == 'add' %}
     switchport trunk allowed vlan add {{ params.vlans | join(',') }}
       {% elif params.action == 'delete' %}
     switchport trunk allowed vlan remove {{ params.vlans | join(',') }}
       {% else %}
     switchport trunk allowed vlan {{ params.vlans | join(',') }}
       {% endif %}
     {% else %}
       {% if params.action == 'add' %}
     switchport trunk allowed vlan add {{ params.vlans }}
       {% elif params.action == 'delete' %}
     switchport trunk allowed vlan remove {{ params.vlans }}
       {% else %}
     switchport trunk allowed vlan {{ params.vlans }}
       {% endif %}
     {% endif %}
    {% endfor %}

Файл с данными (data_files/test_iterable.yml):

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
        vlans: 10

Обратите внимание на последнюю строку: ``vlans: 10``. В данном случае 10
уже не находится в списке, и фильтр join в таком случае не работает. Но,
за счет теста ``is iterable`` (в этом случае результат будет false), в
этом случае шаблон уходит в ветку else.

Результат выполнения:

::

    $ python cfg_gen.py templates/test_iterable.txt data_files/test_iterable.yml
    interface Fa0/1
     switchport trunk allowed vlan add 10,20
    interface Fa0/2
     switchport trunk allowed vlan 10,30
    interface Fa0/3
     switchport trunk allowed vlan remove 10


Такие отступы получились из-за того, что в шаблоне используются
отступы, но не установлено lstrip_blocks=True (он удаляет пробелы и
табы в начале строки).

