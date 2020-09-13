set
---

Внутри шаблона можно присваивать значения переменным. Это могут быть
новые переменные, а могут быть измененные значения переменных, которые
были переданы шаблону.

Таким образом можно запомнить значение, которое, например, было получено
в результате применения нескольких фильтров. И в дальнейшем использовать
имя переменной, а не повторять снова все фильтры.

Пример шаблона templates/set.txt, в котором выражение set используется,
чтобы задать более короткие имена параметрам:

::

    {% for intf, params in trunks | dictsort %}
     {% set vlans = params.vlans %}
     {% set action = params.action %}

    interface {{ intf }}
     {% if vlans is iterable %}
      {% if action == 'add' %}
     switchport trunk allowed vlan add {{ vlans | join(',') }}
      {% elif action == 'delete' %}
     switchport trunk allowed vlan remove {{ vlans | join(',') }}
      {% else %}
     switchport trunk allowed vlan {{ vlans | join(',') }}
      {% endif %}
     {% else %}
      {% if action == 'add' %}
     switchport trunk allowed vlan add {{ vlans }}
      {% elif action == 'delete' %}
     switchport trunk allowed vlan remove {{ vlans }}
      {% else %}
     switchport trunk allowed vlan {{ vlans }}
      {% endif %}
     {% endif %}
    {% endfor %}

Обратите внимание на вторую и третью строки:

::

     {% set vlans = params.vlans %}
     {% set action = params.action %}

Таким образом создаются новые переменные, и дальше используются уже эти
новые значения. Так шаблон выглядит понятней.

Файл с данными (data_files/set.yml):

.. code:: json

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

Результат выполнения:

::

    $ python cfg_gen.py templates/set.txt data_files/set.yml

    interface Fa0/1
     switchport trunk allowed vlan add 10,20

    interface Fa0/2
     switchport trunk allowed vlan 10,30

    interface Fa0/3
     switchport trunk allowed vlan remove 10

