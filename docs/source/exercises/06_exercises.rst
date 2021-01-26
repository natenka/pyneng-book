.. raw:: latex

   \newpage

Задания
=======

.. include:: ./exercises_intro.rst

Задание 6.1
~~~~~~~~~~~

Список mac содержит MAC-адреса в формате XXXX:XXXX:XXXX.
Однако, в оборудовании cisco MAC-адреса используются в формате XXXX.XXXX.XXXX.


Написать код, который преобразует MAC-адреса в формат cisco и добавляет их в новый
список result. Полученный список result вывести на стандартный поток вывода (stdout)
с помощью print.

Ограничение: Все задания надо выполнять используя только пройденные темы.

.. code:: python

    mac = ["aabb:cc80:7000", "aabb:dd80:7340", "aabb:ee80:7000", "aabb:ff80:7000"]

Задание 6.2
~~~~~~~~~~~

1. Запросить у пользователя ввод IP-адреса в формате 10.0.1.1
2. В зависимости от типа адреса (описаны ниже), вывести на стандартный поток вывода:

   * "unicast" - если первый байт в диапазоне 1-223
   * "multicast" - если первый байт в диапазоне 224-239
   * "local broadcast" - если IP-адрес равен 255.255.255.255
   * "unassigned" - если IP-адрес равен 0.0.0.0
   * "unused" - во всех остальных случаях

Ограничение: Все задания надо выполнять используя только пройденные темы.

Задание 6.2a
~~~~~~~~~~~~

Сделать копию скрипта задания 6.2.

Добавить проверку введенного IP-адреса. Адрес считается корректно заданным, если он:

* состоит из 4 чисел (а не букв или других символов)
* числа разделенны точкой
* каждое число в диапазоне от 0 до 255

Если адрес задан неправильно, выводить сообщение: "Неправильный IP-адрес".
Сообщение "Неправильный IP-адрес" должно выводиться только один раз,
даже если несколько пунктов выше не выполнены.


Ограничение: Все задания надо выполнять используя только пройденные темы.

Задание 6.2b
~~~~~~~~~~~~

Сделать копию скрипта задания 6.2a.

Дополнить скрипт:
Если адрес был введен неправильно, запросить адрес снова.

Ограничение: Все задания надо выполнять используя только пройденные темы.

Задание 6.3
~~~~~~~~~~~

В скрипте сделан генератор конфигурации для access-портов.
Сделать аналогичный генератор конфигурации для портов trunk.

В транках ситуация усложняется тем, что VLANов может быть много, и надо понимать,
что с ним делать.
Поэтому в соответствии каждому порту стоит список
и первый (нулевой) элемент списка указывает как воспринимать номера VLAN,
которые идут дальше.

Пример значения и соответствующей команды:

* ["add", "10", "20"] - команда switchport trunk allowed vlan add 10,20
* ["del", "17"] - команда switchport trunk allowed vlan remove 17
* ["only", "11", "30"] - команда switchport trunk allowed vlan 11,30

Задача для портов 0/1, 0/2, 0/4:

* сгенерировать конфигурацию на основе шаблона trunk_template
* с учетом ключевых слов add, del, only

Код не должен привязываться к конкретным номерам портов. То есть, если в словаре
trunk будут другие номера интерфейсов, код должен работать.

Для данных в словаре trunk_template вывод на
стандартный поток вывода должен быть таким:

::

    interface FastEthernet 0/1
     switchport trunk encapsulation dot1q
     switchport mode trunk
     switchport trunk allowed vlan add 10,20
    interface FastEthernet 0/2
     switchport trunk encapsulation dot1q
     switchport mode trunk
     switchport trunk allowed vlan 11,30
    interface FastEthernet 0/4
     switchport trunk encapsulation dot1q
     switchport mode trunk
     switchport trunk allowed vlan remove 17

Ограничение: Все задания надо выполнять используя только пройденные темы.

.. code:: python

    access_template = [
        "switchport mode access",
        "switchport access vlan",
        "spanning-tree portfast",
        "spanning-tree bpduguard enable",
    ]

    trunk_template = [
        "switchport trunk encapsulation dot1q",
        "switchport mode trunk",
        "switchport trunk allowed vlan",
    ]

    access = {"0/12": "10", "0/14": "11", "0/16": "17", "0/17": "150"}
    trunk = {"0/1": ["add", "10", "20"], "0/2": ["only", "11", "30"], "0/4": ["del", "17"]}

    for intf, vlan in access.items():
        print("interface FastEthernet" + intf)
        for command in access_template:
            if command.endswith("access vlan"):
                print(f" {command} {vlan}")
            else:
                print(f" {command}")

