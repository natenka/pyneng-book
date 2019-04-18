Задания
=======

{% include "../exercises\_intro.md" %}

Задание 21.1
~~~~~~~~~~~~

Переделать скрипт cfg\_gen.py в функцию generate\_cfg\_from\_template.

Функция ожидает два аргумента: \* путь к шаблону \* файл с переменными в
формате YAML

Функция должна возвращать конфигурацию, которая была сгенерирована.

Проверить работу функции на шаблоне templates/for.txt и данных
data\_files/for.yml.

.. code:: python

    from jinja2 import Environment, FileSystemLoader
    import yaml
    import sys
    import os

    #$ python cfg_gen.py templates/for.txt data_files/for.yml
    TEMPLATE_DIR, template = os.path.split(sys.argv[1])
    VARS_FILE = sys.argv[2]

    env = Environment(loader=FileSystemLoader(TEMPLATE_DIR),
                      trim_blocks=True, lstrip_blocks=True)
    template = env.get_template(template_file)

    vars_dict = yaml.load(open(VARS_FILE))

    print(template.render(vars_dict))

Задание 21.1a
~~~~~~~~~~~~~

Дополнить функцию generate\_cfg\_from\_template из задания 21.1:

Функция generate\_cfg\_from\_template должна принимать любые аргументы,
которые принимает класс Environment и просто передавать их ему.

То есть, надо добавить возможность контролировать аргументы
trim\_blocks, lstrip\_blocks и любые другие аргументы Environment через
функцию generate\_cfg\_from\_template.

Проверить функциональность на аргументах: \* trim\_blocks \*
lstrip\_blocks

Задание 21.1b
~~~~~~~~~~~~~

Дополнить функцию generate\_cfg\_from\_template из задания 21.1 или
21.1a: \* добавить поддержку разных форматов для файла с данными

Должны поддерживаться такие форматы: \* YAML \* JSON \* словарь Python

Сделать для каждого формата свой параметр функции. Например: \* YAML -
yaml\_file \* JSON - json\_file \* словарь Python - py\_dict

Проверить работу функции на шаблоне templates/for.txt и данных: \*
data\_files/for.yml \* data\_files/for.json \* словаре data\_dict

.. code:: python

    data_dict = {'vlans': {
                            10: 'Marketing',
                            20: 'Voice',
                            30: 'Management'},
                 'ospf': [{'network': '10.0.1.0 0.0.0.255', 'area': 0},
                          {'network': '10.0.2.0 0.0.0.255', 'area': 2},
                          {'network': '10.1.1.0 0.0.0.255', 'area': 0}],
                 'id': 3,
                 'name': 'R3'}

Задание 21.1c
~~~~~~~~~~~~~

Переделать функцию generate\_cfg\_from\_template из задания 21.1, 21.1a
или 21.1b: \* сделать автоматическое распознавание разных форматов для
файла с данными \* для передачи разных типов данных, должен
использоваться один и тот же параметр data

Должны поддерживаться такие форматы: \* YAML - файлы с расширением yml
или yaml \* JSON - файлы с расширением json \* словарь Python

Если не получилось определить тип данных, вывести сообщение
error\_message (перенести текст сообщения в тело функции), завершить
работу функции и вернуть ``None``.

Проверить работу функции на шаблоне templates/for.txt и данных: \*
data\_files/for.yml \* data\_files/for.json \* словаре data\_dict

.. code:: python

    error_message = '''
    Не получилось определить формат данных.
    Поддерживаются файлы с расширением .json, .yml, .yaml и словари Python
    '''

    data_dict = {'vlans': {
                            10: 'Marketing',
                            20: 'Voice',
                            30: 'Management'},
                 'ospf': [{'network': '10.0.1.0 0.0.0.255', 'area': 0},
                          {'network': '10.0.2.0 0.0.0.255', 'area': 2},
                          {'network': '10.1.1.0 0.0.0.255', 'area': 0}],
                 'id': 3,
                 'name': 'R3'}

Задание 21.2
~~~~~~~~~~~~

На основе конфигурации config\_r1.txt, создать шаблоны: \*
templates/cisco\_base.txt - в нём должны быть все строки, кроме
настройки alias и event manager \* имя хоста должно быть переменной
hostname \* templates/alias.txt - в этот шаблон перенести все alias \*
templates/eem\_int\_desc.txt - в этом шаблоне должен быть event manager
applet

В шаблонах templates/alias.txt и templates/eem\_int\_desc.txt переменных
нет.

Создать шаблон templates/cisco\_router\_base.txt.

В шаблон должно быть включено содержимое шаблонов: \*
templates/cisco\_base.txt \* templates/alias.txt \*
templates/eem\_int\_desc.txt

При этом, нельзя копировать текст шаблонов.

Проверьте шаблон templates/cisco\_router\_base.txt, с помощью функции
generate\_cfg\_from\_template из задания 21.1-21.1c. Не копируйте код
функции.

В качестве данных, используйте файл data\_files/router\_info.yml

Задание 21.3
~~~~~~~~~~~~

Создайте шаблон templates/ospf.txt на основе конфигурации OSPF в файле
cisco\_ospf.txt. Пример конфигурации дан, чтобы напомнить синтаксис.

Какие значения должны быть переменными: \* номер процесса. Имя
переменной - ``process`` \* router-id. Имя переменной - ``router_id`` \*
reference-bandwidth. Имя переменной - ``ref_bw`` \* интерфейсы, на
которых нужно включить OSPF. Имя переменной - ``ospf_intf`` \* на месте
этой переменной ожидается список словарей с такими ключами: \* ``name``
- имя интерфейса, вида Fa0/1, VLan10, Gi0/0 \* ``ip`` - IP-адрес
интерфейса, вида 10.0.1.1 \* ``area`` - номер зоны \* ``passive`` -
является ли интерфейс пассивным. Допустимые значения: True или False

Для всех интерфейсов в списке ospf\_intf, надо сгенерировать строки:

::

     network x.x.x.x 0.0.0.0 area x

Если интерфейс пассивный, для него должна быть добавлена строка:

::

     passive-interface x

Для интерфейсов, которые не являются пассивными, в режиме конфигурации
интерфейса, надо добавить строку:

::

     ip ospf hello-interval 1

Все команды должны быть в соответствующих режимах.

Проверьте получившийся шаблон templates/ospf.txt, на данных в файле
data\_files/ospf.yml, с помощью функции generate\_cfg\_from\_template из
задания 21.1-21.1c. Не копируйте код функции.

Задание 21.3a
~~~~~~~~~~~~~

Измените шаблон templates/ospf.txt таким образом, чтобы для
перечисленных переменных были указаны значения по умолчанию, которые
используются в том случае, если переменная не задана.

Не использовать для этого выражения if/else.

Задать в шаблоне значения по умолчанию для таких переменных: \* process
- значение по умолчанию 1 \* ref\_bw - значение по умолчанию 10000

Проверьте получившийся шаблон templates/ospf.txt, на данных в файле
data\_files/ospf2.yml, с помощью функции generate\_cfg\_from\_template
из задания 21.1-21.1c. Не копируйте код функции.

Задание 21.3b
~~~~~~~~~~~~~

Измените шаблон templates/ospf.txt из задания 21.3a таким образом, чтобы
для перечисленных переменных были указаны значения по умолчанию, которые
используются в том случае, если переменная не задана или, если в
переменной пустое значение.

Не использовать для этого выражения if/else.

Задать в шаблоне значения по умолчанию для таких переменных: \* process
- значение по умолчанию 1 \* ref\_bw - значение по умолчанию 10000

Проверьте получившийся шаблон templates/ospf.txt, на данных в файле
data\_files/ospf3.yml, с помощью функции generate\_cfg\_from\_template
из задания 21.1-21.1c. Не копируйте код функции.

Задание 21.4
~~~~~~~~~~~~

Создайте шаблон templates/add\_vlan\_to\_switch.txt, который будет
использоваться при необходимости добавить VLAN на коммутатор.

В шаблоне должны поддерживаться возможности: \* добавления VLAN и имени
VLAN \* добавления VLAN как access, на указанном интерфейсе \*
добавления VLAN в список разрешенных, на указанные транки

Если VLAN необходимо добавить как access, то надо настроить и режим
интерфейса и добавить его в VLAN:

::

    interface Gi0/1
     switchport mode access
     switchport access vlan 5

Для транков, необходимо только добавить VLAN в список разрешенных:

::

    interface Gi0/10
     switchport trunk allowed vlan add 5

Имена переменных надо выбрать на основании примера данных, в файле
data\_files/add\_vlan\_to\_switch.yaml.

Проверьте шаблон templates/add\_vlan\_to\_switch.txt на данных в файле
data\_files/add\_vlan\_to\_switch.yaml, с помощью функции
generate\_cfg\_from\_template из задания 21.1-21.1c. Не копируйте код
функции.
