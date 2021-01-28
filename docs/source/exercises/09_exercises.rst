.. raw:: latex

   \newpage

Задания
=======

.. include:: ./exercises_intro.rst

Задание 9.1
~~~~~~~~~~~

Создать функцию, которая генерирует конфигурацию для access-портов.

Функция ожидает такие аргументы:

1. словарь с соответствием интерфейс-VLAN такого вида:

.. code:: python

    {"FastEthernet0/12": 10,
     "FastEthernet0/14": 11,
     "FastEthernet0/16": 17}

2. шаблон конфигурации access-портов в виде списка команд (список access_mode_template)

Функция должна возвращать список всех портов в режиме access
с конфигурацией на основе шаблона access_mode_template.
В конце строк в списке не должно быть символа перевода строки.

В этом задании заготовка для функции уже сделана и надо только продолжить писать само тело функции.


Пример итогового списка (перевод строки после каждого элемента сделан для удобства чтения):

.. code:: python

    [
    "interface FastEthernet0/12",
    "switchport mode access",
    "switchport access vlan 10",
    "switchport nonegotiate",
    "spanning-tree portfast",
    "spanning-tree bpduguard enable",
    "interface FastEthernet0/17",
    "switchport mode access",
    "switchport access vlan 150",
    "switchport nonegotiate",
    "spanning-tree portfast",
    "spanning-tree bpduguard enable",
    ...]

Проверить работу функции на примере словаря access_config
и списка команд access_mode_template.
Если предыдущая проверка прошла успешно, проверить работу функции еще раз на словаре
access_config_2 и убедиться, что в итоговом списке правильные номера интерфейсов
и вланов.

Ограничение: Все задания надо выполнять используя только пройденные темы.

.. code:: python

    access_mode_template = [
        "switchport mode access", "switchport access vlan",
        "switchport nonegotiate", "spanning-tree portfast",
        "spanning-tree bpduguard enable"
    ]

    access_config = {
        "FastEthernet0/12": 10,
        "FastEthernet0/14": 11,
        "FastEthernet0/16": 17
    }

    access_config_2 = {
        "FastEthernet0/03": 100,
        "FastEthernet0/07": 101,
        "FastEthernet0/09": 107,
    }

    def generate_access_config(intf_vlan_mapping, access_template):
        """
        intf_vlan_mapping - словарь с соответствием интерфейс-VLAN такого вида:
            {"FastEthernet0/12": 10,
             "FastEthernet0/14": 11,
             "FastEthernet0/16": 17}
        access_template - список команд для порта в режиме access

        Возвращает список всех портов в режиме access с конфигурацией на основе шаблона
        """


Задание 9.1a
~~~~~~~~~~~~

Сделать копию функции generate_access_config из задания 9.1.

Дополнить скрипт: ввести дополнительный параметр, который контролирует будет ли настроен port-security:

* имя параметра "psecurity"
* по умолчанию значение None
* для настройки port-security, как значение надо передать список команд port-security (находятся в списке port_security_template)

Функция должна возвращать список всех портов в режиме access
с конфигурацией на основе шаблона access_mode_template и шаблона port_security_template, если он был передан.
В конце строк в списке не должно быть символа перевода строки.


Проверить работу функции на примере словаря access_config,
с генерацией конфигурации port-security и без.

Пример вызова функции:

::

    print(generate_access_config(access_config, access_mode_template))
    print(generate_access_config(access_config, access_mode_template, port_security_template))


Ограничение: Все задания надо выполнять используя только пройденные темы.

.. code:: python

    access_mode_template = [
        "switchport mode access", "switchport access vlan",
        "switchport nonegotiate", "spanning-tree portfast",
        "spanning-tree bpduguard enable"
    ]

    port_security_template = [
        "switchport port-security maximum 2",
        "switchport port-security violation restrict",
        "switchport port-security"
    ]

    access_config = {"FastEthernet0/12": 10, "FastEthernet0/14": 11, "FastEthernet0/16": 17}


Задание 9.2
~~~~~~~~~~~

Создать функцию generate_trunk_config, которая генерирует конфигурацию для trunk-портов.

У функции должны быть такие параметры:

1. intf_vlan_mapping: ожидает как аргумент словарь с соответствием интерфейс-VLANы такого вида:

.. code:: python

    {"FastEthernet0/1": [10, 20],
     "FastEthernet0/2": [11, 30],
     "FastEthernet0/4": [17]}

2. trunk_template: ожидает как аргумент шаблон конфигурации trunk-портов в виде списка команд (список trunk_mode_template)

Функция должна возвращать список команд с конфигурацией
на основе указанных портов и шаблона trunk_mode_template.
В конце строк в списке не должно быть символа перевода строки.

Проверить работу функции на примере словаря trunk_config и списка команд trunk_mode_template.
Если эта проверка прошла успешно, проверить работу функции еще раз на словаре trunk_config_2
и убедится, что в итоговом списке правильные номера интерфейсов и вланов.


Пример итогового списка (перевод строки после каждого элемента сделан для удобства чтения):

.. code:: python

    [
    "interface FastEthernet0/1",
    "switchport mode trunk",
    "switchport trunk native vlan 999",
    "switchport trunk allowed vlan 10,20,30",
    "interface FastEthernet0/2",
    "switchport mode trunk",
    "switchport trunk native vlan 999",
    "switchport trunk allowed vlan 11,30",
    ...]


Ограничение: Все задания надо выполнять используя только пройденные темы.

.. code:: python

    trunk_mode_template = [
        "switchport mode trunk", "switchport trunk native vlan 999",
        "switchport trunk allowed vlan"
    ]

    trunk_config = {
        "FastEthernet0/1": [10, 20, 30],
        "FastEthernet0/2": [11, 30],
        "FastEthernet0/4": [17]
    }


Задание 9.2a
~~~~~~~~~~~~

Сделать копию функции generate_trunk_config из задания 9.2

Изменить функцию таким образом, чтобы она возвращала не список команд, а словарь:

* ключи: имена интерфейсов, вида "FastEthernet0/1"
* значения: список команд, который надо выполнить на этом интерфейсе

Проверить работу функции на примере словаря trunk_config и шаблона trunk_mode_template.

Ограничение: Все задания надо выполнять используя только пройденные темы.

.. code:: python

    trunk_mode_template = [
        "switchport mode trunk", "switchport trunk native vlan 999",
        "switchport trunk allowed vlan"
    ]

    trunk_config = {
        "FastEthernet0/1": [10, 20, 30],
        "FastEthernet0/2": [11, 30],
        "FastEthernet0/4": [17]
    }


Задание 9.3
~~~~~~~~~~~

Создать функцию get_int_vlan_map, которая обрабатывает конфигурационный файл коммутатора
и возвращает кортеж из двух словарей:

1. словарь портов в режиме access, где ключи номера портов, а значения access VLAN (числа):

.. code:: python

    {"FastEthernet0/12": 10,
     "FastEthernet0/14": 11,
     "FastEthernet0/16": 17}

2. словарь портов в режиме trunk, где ключи номера портов, а значения список разрешенных VLAN (список чисел):

.. code:: python
    {"FastEthernet0/1": [10, 20],
     "FastEthernet0/2": [11, 30],
     "FastEthernet0/4": [17]}

У функции должен быть один параметр config_filename, который ожидает как аргумент имя конфигурационного файла.

Проверить работу функции на примере файла config_sw1.txt

Ограничение: Все задания надо выполнять используя только пройденные темы.


Задание 9.3a
~~~~~~~~~~~~

Сделать копию функции get_int_vlan_map из задания 9.3.

Дополнить функцию: добавить поддержку конфигурации, когда настройка access-порта выглядит так:

::

    interface FastEthernet0/20
     switchport mode access
     duplex auto

То есть, порт находится в VLAN 1

В таком случае, в словарь портов должна добавляться информация, что порт в VLAN 1

.. code:: python
      {"FastEthernet0/12": 10,
       "FastEthernet0/14": 11,
       "FastEthernet0/20": 1}

У функции должен быть один параметр config_filename, который ожидает как аргумент имя конфигурационного файла.

Проверить работу функции на примере файла config_sw2.txt

Ограничение: Все задания надо выполнять используя только пройденные темы.


Задание 9.4
~~~~~~~~~~~

Создать функцию convert_config_to_dict, которая обрабатывает конфигурационный файл коммутатора и возвращает словарь:

* Все команды верхнего уровня (глобального режима конфигурации), будут ключами.
* Если у команды верхнего уровня есть подкоманды, они должны быть в значении у соответствующего ключа, в виде списка (пробелы в начале строки надо удалить).
* Если у команды верхнего уровня нет подкоманд, то значение будет пустым списком

У функции должен быть один параметр config_filename, который ожидает как аргумент имя конфигурационного файла.

При обработке конфигурационного файла, надо игнорировать строки, которые начинаются с "!",
а также строки в которых содержатся слова из списка ignore.
Для проверки надо ли игнорировать строку, использовать функцию ignore_command.

Проверить работу функции на примере файла config_sw1.txt

Часть словаря, который должна возвращать функция (полный вывод можно посмотреть
в тесте test_task_9_4.py):

.. code:: python

    {
        "version 15.0": [],
        "service timestamps debug datetime msec": [],
        "service timestamps log datetime msec": [],
        "no service password-encryption": [],
        "hostname sw1": [],
        "interface FastEthernet0/0": [
            "switchport mode access",
            "switchport access vlan 10",
        ],
        "interface FastEthernet0/1": [
            "switchport trunk encapsulation dot1q",
            "switchport trunk allowed vlan 100,200",
            "switchport mode trunk",
        ],
        "interface FastEthernet0/2": [
            "switchport mode access",
            "switchport access vlan 20",
        ],
    }

Ограничение: Все задания надо выполнять используя только пройденные темы.

.. code:: python

    ignore = ["duplex", "alias", "Current configuration"]


    def ignore_command(command, ignore):
        """
        Функция проверяет содержится ли в команде слово из списка ignore.

        command - строка. Команда, которую надо проверить
        ignore - список. Список слов

        Возвращает
        * True, если в команде содержится слово из списка ignore
        * False - если нет
        """
        ignore_status = False
        for word in ignore:
            if word in command:
                ignore_status = True
        return ignore_status
