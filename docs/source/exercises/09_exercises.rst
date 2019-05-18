Задания
=======

.. include:: ./exercises_intro.rst

Задание 9.1
~~~~~~~~~~~

Создать функцию, которая генерирует конфигурацию для access-портов.

Функция ожидает, как аргумент, словарь access-портов, вида:

.. code:: python

    {'FastEthernet0/12':10,
     'FastEthernet0/14':11,
     'FastEthernet0/16':17,
     'FastEthernet0/17':150}

Функция должна возвращать список всех портов в режиме access с
конфигурацией на основе шаблона access\_template.

В конце строк в списке не должно быть символа перевода строки.

Пример итогового списка (перевод строки после каждого элемента сделан
для удобства чтения):

.. code:: python

    [
    'interface FastEthernet0/12',
    'switchport mode access',
    'switchport access vlan 10',
    'switchport nonegotiate',
    'spanning-tree portfast',
    'spanning-tree bpduguard enable',
    'interface FastEthernet0/17',
    'switchport mode access',
    'switchport access vlan 150',
    'switchport nonegotiate',
    'spanning-tree portfast',
    'spanning-tree bpduguard enable',
    ...]

Проверить работу функции на примере словаря access\_dict.

Ограничение: Все задания надо выполнять используя только пройденные
темы.

.. code:: python

    def generate_access_config(access):
        '''
        access - словарь access-портов,
        для которых необходимо сгенерировать конфигурацию, вида:
            { 'FastEthernet0/12':10,
              'FastEthernet0/14':11,
              'FastEthernet0/16':17}
        
        Возвращает список всех портов в режиме access
        с конфигурацией на основе шаблона
        '''
        access_template = ['switchport mode access',
                           'switchport access vlan',
                           'switchport nonegotiate',
                           'spanning-tree portfast',
                           'spanning-tree bpduguard enable']


    access_dict = { 'FastEthernet0/12':10,
                    'FastEthernet0/14':11,
                    'FastEthernet0/16':17,
                    'FastEthernet0/17':150 }

Задание 9.1a
~~~~~~~~~~~~

Сделать копию скрипта задания 9.1.

Дополнить скрипт: \* ввести дополнительный параметр, который
контролирует будет ли настроен port-security \* имя параметра
'psecurity' \* по умолчанию значение False

Проверить работу функции на примере словаря access\_dict, с генерацией
конфигурации port-security и без.

Ограничение: Все задания надо выполнять используя только пройденные
темы.

.. code:: python

    def generate_access_config(access):
        '''
        access - словарь access-портов,
        для которых необходимо сгенерировать конфигурацию, вида:
            { 'FastEthernet0/12':10,
              'FastEthernet0/14':11,
              'FastEthernet0/16':17 }
        
        psecurity - контролирует нужна ли настройка Port Security. По умолчанию значение False
            - если значение True, то настройка выполняется с добавлением шаблона port_security
            - если значение False, то настройка не выполняется
        
        Возвращает список всех команд, которые были сгенерированы на основе шаблона
        '''

        access_template = ['switchport mode access',
                           'switchport access vlan',
                           'switchport nonegotiate',
                           'spanning-tree portfast',
                           'spanning-tree bpduguard enable']

        port_security = ['switchport port-security maximum 2',
                         'switchport port-security violation restrict',
                         'switchport port-security']

    access_dict = { 'FastEthernet0/12':10,
                    'FastEthernet0/14':11,
                    'FastEthernet0/16':17,
                    'FastEthernet0/17':150 }

Задание 9.1b
~~~~~~~~~~~~

Сделать копию скрипта задания 9.1a.

Изменить скрипт таким образом, чтобы функция возвращала не список
команд, а словарь: \* ключи: имена интерфейсов, вида 'FastEthernet0/12'
\* значения: список команд, который надо выполнить на этом интерфейсе:
``python       ['switchport mode access',        'switchport access vlan 10',        'switchport nonegotiate',        'spanning-tree portfast',        'spanning-tree bpduguard enable']``

Проверить работу функции на примере словаря access\_dict, с генерацией
конфигурации port-security и без.

Ограничение: Все задания надо выполнять используя только пройденные
темы.

.. code:: python

    def generate_access_config(access):
        '''
        access - словарь access-портов,
        для которых необходимо сгенерировать конфигурацию, вида:
            { 'FastEthernet0/12':10,
              'FastEthernet0/14':11,
              'FastEthernet0/16':17 }
        
        psecurity - контролирует нужна ли настройка Port Security. По умолчанию значение False
            - если значение True, то настройка выполняется с добавлением шаблона port_security
            - если значение False, то настройка не выполняется
        
        Функция возвращает словарь:
        - ключи: имена интерфейсов, вида 'FastEthernet0/1'
        - значения: список команд, который надо выполнить на этом интерфейсе
        '''

        access_template = ['switchport mode access',
                           'switchport access vlan',
                           'switchport nonegotiate',
                           'spanning-tree portfast',
                           'spanning-tree bpduguard enable']

        port_security = ['switchport port-security maximum 2',
                         'switchport port-security violation restrict',
                         'switchport port-security']

    access_dict = { 'FastEthernet0/12':10,
                    'FastEthernet0/14':11,
                    'FastEthernet0/16':17,
                    'FastEthernet0/17':150 }

Задание 9.2
~~~~~~~~~~~

Создать функцию, которая генерирует конфигурацию для trunk-портов.

Параметр trunk - это словарь trunk-портов.

Словарь trunk имеет такой формат (тестовый словарь trunk\_dict уже
создан):

.. code:: python

    { 'FastEthernet0/1':[10,20],
      'FastEthernet0/2':[11,30],
      'FastEthernet0/4':[17] }

Функция должна возвращать список команд с конфигурацией на основе
указанных портов и шаблона trunk\_template.

В конце строк в списке не должно быть символа перевода строки.

Проверить работу функции на примере словаря trunk\_dict.

Ограничение: Все задания надо выполнять используя только пройденные
темы.

.. code:: python

    def generate_trunk_config(trunk):
        '''
        trunk - словарь trunk-портов для которых необходимо сгенерировать конфигурацию.
        
        Возвращает список всех команд, которые были сгенерированы на основе шаблона
        '''
        trunk_template = ['switchport trunk encapsulation dot1q',
                          'switchport mode trunk',
                          'switchport trunk native vlan 999',
                          'switchport trunk allowed vlan']

    trunk_dict = { 'FastEthernet0/1':[10,20,30],
                   'FastEthernet0/2':[11,30],
                   'FastEthernet0/4':[17] }

Задание 9.2a
~~~~~~~~~~~~

Сделать копию скрипта задания 9.2

Изменить скрипт таким образом, чтобы функция возвращала не список
команд, а словарь: \* ключи: имена интерфейсов, вида 'FastEthernet0/1'
\* значения: список команд, который надо выполнить на этом интерфейсе

Проверить работу функции на примере словаря trunk\_dict.

Ограничение: Все задания надо выполнять используя только пройденные
темы.

.. code:: python

    def generate_trunk_config(trunk):
        '''
        trunk - словарь trunk-портов,
        для которых необходимо сгенерировать конфигурацию, вида:
            { 'FastEthernet0/1':[10,20],
              'FastEthernet0/2':[11,30],
              'FastEthernet0/4':[17] }
        
        Возвращает словарь:
        - ключи: имена интерфейсов, вида 'FastEthernet0/1'
        - значения: список команд, который надо выполнить на этом интерфейсе
        '''
        trunk_template = ['switchport trunk encapsulation dot1q',
                          'switchport mode trunk',
                          'switchport trunk native vlan 999',
                          'switchport trunk allowed vlan']

    trunk_dict = { 'FastEthernet0/1':[10,20,30],
                   'FastEthernet0/2':[11,30],
                   'FastEthernet0/4':[17] }

Задание 9.3
~~~~~~~~~~~

Создать функцию get\_int\_vlan\_map, которая обрабатывает
конфигурационный файл коммутатора и возвращает два объекта: \* словарь
портов в режиме access, где ключи номера портов, а значения access VLAN:

``python {'FastEthernet0/12':10,  'FastEthernet0/14':11,  'FastEthernet0/16':17}``

-  словарь портов в режиме trunk, где ключи номера портов, а значения
   список разрешенных VLAN:

.. code:: python

     {'FastEthernet0/1':[10,20],
      'FastEthernet0/2':[11,30],
      'FastEthernet0/4':[17]}

Функция ожидает в качестве аргумента имя конфигурационного файла.

Проверить работу функции на примере файла config\_sw1.txt

Ограничение: Все задания надо выполнять используя только пройденные
темы.

Задание 9.3a
~~~~~~~~~~~~

Сделать копию скрипта задания 9.3.

Дополнить скрипт: \* добавить поддержку конфигурации, когда настройка
access-порта выглядит так:

::

    interface FastEthernet0/20
      switchport mode access
      duplex auto

То есть, порт находится в VLAN 1

В таком случае, в словарь портов должна добавляться информация, что порт
в VLAN 1

Пример словаря:

.. code:: python

    {'FastEthernet0/12':10,
     'FastEthernet0/14':11,
     'FastEthernet0/20':1 }

Функция ожидает в качестве аргумента имя конфигурационного файла.

Проверить работу функции на примере файла config\_sw2.txt

Ограничение: Все задания надо выполнять используя только пройденные
темы.

Задание 9.4
~~~~~~~~~~~

Создать функцию, которая обрабатывает конфигурационный файл коммутатора
и возвращает словарь: \* Все команды верхнего уровня (глобального режима
конфигурации), будут ключами. \* Если у команды верхнего уровня есть
подкоманды, они должны быть в значении у соответствующего ключа, в виде
списка (пробелы вначале можно оставлять). \* Если у команды верхнего
уровня нет подкоманд, то значение будет пустым списком

Функция ожидает в качестве аргумента имя конфигурационного файла.

Проверить работу функции на примере файла config\_sw1.txt

При обработке конфигурационного файла, надо игнорировать строки, которые
начинаются с '!', а также строки в которых содержатся слова из списка
ignore.

Для проверки надо ли игнорировать строку, использовать функцию
ignore\_command.

Ограничение: Все задания надо выполнять используя только пройденные
темы.

.. code:: python

    ignore = ['duplex', 'alias', 'Current configuration']

    def ignore_command(command, ignore):
        '''
        Функция проверяет содержится ли в команде слово из списка ignore.

        command - строка. Команда, которую надо проверить
        ignore - список. Список слов

        Возвращает True, если в команде содержится слово из списка ignore, False - если нет
        '''
        return any(word in command for word in ignore)

Задание 9.4a
~~~~~~~~~~~~

Задача такая же, как и задании 9.4. Проверить работу функции надо на
примере файла config\_r1.txt

Обратите внимание на конфигурационный файл. В нём есть разделы с большей
вложенностью, например, разделы: \* interface Ethernet0/3.100 \* router
bgp 100

Надо чтобы функция config\_to\_dict обрабатывала следующий уровень
вложенности. При этом, не привязываясь к конкретным разделам. Она должна
быть универсальной, и сработать, если это будут другие разделы.

Если уровня вложенности два: \* то команды верхнего уровня будут ключами
словаря, \* а команды подуровней - списками

Если уровня вложенности три: \* самый вложенный уровень должен быть
списком, \* а остальные - словарями.

На примере interface Ethernet0/3.100

.. code:: python

    {'interface Ethernet0/3.100':{
                        'encapsulation dot1Q 100':[],
                        'xconnect 10.2.2.2 12100 encapsulation mpls':
                            ['backup peer 10.4.4.4 14100',
                             'backup delay 1 1']}}

Ограничение: Все задания надо выполнять используя только пройденные
темы.

.. code:: python

    ignore = ['duplex', 'alias', 'Current configuration']

    def check_ignore(command, ignore):
        '''
        Функция проверяет содержится ли в команде слово из списка ignore.

        command - строка. Команда, которую надо проверить
        ignore - список. Список слов

        Возвращает True, если в команде содержится слово из списка ignore, False - если нет
        
        '''
        return any(word in command for word in ignore)

