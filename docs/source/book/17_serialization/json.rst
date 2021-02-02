Работа с файлами в формате JSON
-------------------------------

**JSON (JavaScript Object Notation)** - это текстовый формат для
хранения и обмена данными.

`JSON <https://ru.wikipedia.org/wiki/JSON>`__ по синтаксису очень похож
на Python и достаточно удобен для восприятия.

Как и в случае с CSV, в Python есть модуль, который позволяет легко
записывать и читать данные в формате JSON.

Чтение
~~~~~~

Файл sw_templates.json:

.. code:: json

    {
      "access": [
        "switchport mode access",
        "switchport access vlan",
        "switchport nonegotiate",
        "spanning-tree portfast",
        "spanning-tree bpduguard enable"
      ],
      "trunk": [
        "switchport trunk encapsulation dot1q",
        "switchport mode trunk",
        "switchport trunk native vlan 999",
        "switchport trunk allowed vlan"
      ]
    }

Для чтения в модуле json есть два метода: 

* ``json.load`` - метод считывает файл в формате JSON и возвращает объекты Python 
* ``json.loads`` - метод считывает строку в формате JSON и возвращает объекты Python

``json.load``
^^^^^^^^^^^

Чтение файла в формате JSON в объект Python (файл json_read_load.py):

.. code:: python

    import json

    with open('sw_templates.json') as f:
        templates = json.load(f)

    print(templates)

    for section, commands in templates.items():
        print(section)
        print('\n'.join(commands))

Вывод будет таким:

.. code:: python

    $ python json_read_load.py
    {'access': ['switchport mode access', 'switchport access vlan', 'switchport nonegotiate', 'spanning-tree portfast', 'spanning-tree bpduguard enable'], 'trunk': ['switchport trunk encapsulation dot1q', 'switchport mode trunk', 'switchport trunk native vlan 999', 'switchport trunk allowed vlan']}
    access
    switchport mode access
    switchport access vlan
    switchport nonegotiate
    spanning-tree portfast
    spanning-tree bpduguard enable
    trunk
    switchport trunk encapsulation dot1q
    switchport mode trunk
    switchport trunk native vlan 999
    switchport trunk allowed vlan

``json.loads``
^^^^^^^^^^^^

Считывание строки в формате JSON в объект Python (файл
json_read_loads.py):

.. code:: python

    import json

    with open('sw_templates.json') as f:
        file_content = f.read()
        templates = json.loads(file_content)

    print(templates)

    for section, commands in templates.items():
        print(section)
        print('\n'.join(commands))

Результат будет аналогичен предыдущему выводу.

Запись
~~~~~~

Запись файла в формате JSON также осуществляется достаточно легко.

Для записи информации в формате JSON в модуле json также два метода: 

* ``json.dump`` - метод записывает объект Python в файл в формате JSON 
* ``json.dumps`` - метод возвращает строку в формате JSON

``json.dumps``
^^^^^^^^^^^^

Преобразование объекта в строку в формате JSON (json_write_dumps.py):

.. code:: python

    import json

    trunk_template = [
        'switchport trunk encapsulation dot1q', 'switchport mode trunk',
        'switchport trunk native vlan 999', 'switchport trunk allowed vlan'
    ]

    access_template = [
        'switchport mode access', 'switchport access vlan',
        'switchport nonegotiate', 'spanning-tree portfast',
        'spanning-tree bpduguard enable'
    ]

    to_json = {'trunk': trunk_template, 'access': access_template}

    with open('sw_templates.json', 'w') as f:
        f.write(json.dumps(to_json))

    with open('sw_templates.json') as f:
        print(f.read())


Метод ``json.dumps`` подходит для ситуаций, когда надо вернуть строку в
формате JSON. Например, чтобы передать ее API.

``json.dump``
^^^^^^^^^^^

Запись объекта Python в файл в формате JSON (файл json_write_dump.py):

.. code:: python

    import json

    trunk_template = [
        'switchport trunk encapsulation dot1q', 'switchport mode trunk',
        'switchport trunk native vlan 999', 'switchport trunk allowed vlan'
    ]

    access_template = [
        'switchport mode access', 'switchport access vlan',
        'switchport nonegotiate', 'spanning-tree portfast',
        'spanning-tree bpduguard enable'
    ]

    to_json = {'trunk': trunk_template, 'access': access_template}

    with open('sw_templates.json', 'w') as f:
        json.dump(to_json, f)

    with open('sw_templates.json') as f:
        print(f.read())

Когда нужно записать информацию в формате JSON в файл, лучше
использовать метод dump.

Дополнительные параметры методов записи
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Методам dump и dumps можно передавать дополнительные параметры для
управления форматом вывода.

По умолчанию эти методы записывают информацию в компактном
представлении. Как правило, когда данные используются другими
программами, визуальное представление данных не важно. Если же данные в
файле нужно будет считать человеку, такой формат не очень удобно
воспринимать.

К счастью, модуль json позволяет управлять подобными вещами.

Передав дополнительные параметры методу dump (или методу dumps), можно
получить более удобный для чтения вывод (файл json_write_indent.py):

.. code:: python

    import json

    trunk_template = [
        'switchport trunk encapsulation dot1q', 'switchport mode trunk',
        'switchport trunk native vlan 999', 'switchport trunk allowed vlan'
    ]

    access_template = [
        'switchport mode access', 'switchport access vlan',
        'switchport nonegotiate', 'spanning-tree portfast',
        'spanning-tree bpduguard enable'
    ]

    to_json = {'trunk': trunk_template, 'access': access_template}

    with open('sw_templates.json', 'w') as f:
        json.dump(to_json, f, sort_keys=True, indent=2)

    with open('sw_templates.json') as f:
        print(f.read())

Теперь содержимое файла sw_templates.json выглядит так:

::

    {
      "access": [
        "switchport mode access",
        "switchport access vlan",
        "switchport nonegotiate",
        "spanning-tree portfast",
        "spanning-tree bpduguard enable"
      ],
      "trunk": [
        "switchport trunk encapsulation dot1q",
        "switchport mode trunk",
        "switchport trunk native vlan 999",
        "switchport trunk allowed vlan"
      ]
    }

Изменение типа данных
^^^^^^^^^^^^^^^^^^^^^

Еще один важный аспект преобразования данных в формат JSON: данные не
всегда будут того же типа, что исходные данные в Python.

Например, кортежи при записи в JSON превращаются в списки:

.. code:: python

    In [1]: import json

    In [2]: trunk_template = ('switchport trunk encapsulation dot1q',
       ...:                   'switchport mode trunk',
       ...:                   'switchport trunk native vlan 999',
       ...:                   'switchport trunk allowed vlan')

    In [3]: print(type(trunk_template))
    <class 'tuple'>

    In [4]: with open('trunk_template.json', 'w') as f:
       ...:     json.dump(trunk_template, f, sort_keys=True, indent=2)
       ...:

    In [5]: cat trunk_template.json
    [
      "switchport trunk encapsulation dot1q",
      "switchport mode trunk",
      "switchport trunk native vlan 999",
      "switchport trunk allowed vlan"
    ]
    In [6]: templates = json.load(open('trunk_template.json'))

    In [7]: type(templates)
    Out[7]: list

    In [8]: print(templates)
    ['switchport trunk encapsulation dot1q', 'switchport mode trunk', 'switchport trunk native vlan 999', 'switchport trunk allowed vlan']

Так происходит из-за того, что в JSON используются другие типы данных и
не для всех типов данных Python есть соответствия.

Таблица конвертации данных Python в JSON:

+---------------+----------+
| Python        | JSON     |
+===============+==========+
| dict          | object   |
+---------------+----------+
| list, tuple   | array    |
+---------------+----------+
| str           | string   |
+---------------+----------+
| int, float    | number   |
+---------------+----------+
| True          | true     |
+---------------+----------+
| False         | false    |
+---------------+----------+
| None          | null     |
+---------------+----------+

Таблица конвертации JSON в данные Python:

+-----------------+----------+
| JSON            | Python   |
+=================+==========+
| object          | dict     |
+-----------------+----------+
| array           | list     |
+-----------------+----------+
| string          | str      |
+-----------------+----------+
| number (int)    | int      |
+-----------------+----------+
| number (real)   | float    |
+-----------------+----------+
| true            | True     |
+-----------------+----------+
| false           | False    |
+-----------------+----------+
| null            | None     |
+-----------------+----------+

Ограничение по типам данных
^^^^^^^^^^^^^^^^^^^^^^^^^^^

В формат JSON нельзя записать словарь, у которого ключи - кортежи:

.. code:: python

    In [23]: to_json = {('trunk', 'cisco'): trunk_template, 'access': access_template}

    In [24]: with open('sw_templates.json', 'w') as f:
        ...:     json.dump(to_json, f)
        ...:
    ...
    TypeError: key ('trunk', 'cisco') is not a string

С помощью дополнительного параметра можно игнорировать подобные
ключи:

.. code:: python

    In [25]: to_json = {('trunk', 'cisco'): trunk_template, 'access': access_template}

    In [26]: with open('sw_templates.json', 'w') as f:
        ...:     json.dump(to_json, f, skipkeys=True)
        ...:
        ...:

    In [27]: cat sw_templates.json
    {"access": ["switchport mode access", "switchport access vlan", "switchport nonegotiate", "spanning-tree portfast", "spanning-tree bpduguard enable"]}

Кроме того, в JSON ключами словаря могут быть только строки. Но, если в
словаре Python использовались числа, ошибки не будет. Вместо этого
выполнится конвертация чисел в строки:

.. code:: python

    In [28]: d = {1: 100, 2: 200}

    In [29]: json.dumps(d)
    Out[29]: '{"1": 100, "2": 200}'

