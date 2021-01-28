.. raw:: latex

   \newpage

Задания
=======

.. include:: ./exercises_intro.rst

Задание 18.1
~~~~~~~~~~~~

Создать функцию send_show_command.

Функция подключается по SSH (с помощью netmiko) к ОДНОМУ устройству и выполняет указанную команду.

Параметры функции:

* device - словарь с параметрами подключения к устройству
* command - команда, которую надо выполнить

Функция возвращает строку с выводом команды.

Скрипт должен отправлять команду command на все устройства из файла devices.yaml с помощью функции send_show_command (эта часть кода написана).

.. code:: python

    import yaml



    if __name__ == "__main__":
        command = "sh ip int br"
        with open("devices.yaml") as f:
            devices = yaml.safe_load(f)

        for dev in devices:
            print(send_show_command(dev, command))

Задание 18.1a
~~~~~~~~~~~~~

Скопировать функцию send_show_command из задания 18.1 и переделать ее таким образом,
чтобы обрабатывалось исключение, которое генерируется при ошибке аутентификации на устройстве.

При возникновении ошибки, на стандартный поток вывода должно выводиться сообщение исключения.

Для проверки измените пароль на устройстве или в файле devices.yaml.

Задание 18.1b
~~~~~~~~~~~~~

Скопировать функцию send_show_command из задания 18.1a и переделать ее таким образом,
чтобы обрабатывалось не только исключение, которое генерируется
при ошибке аутентификации на устройстве, но и исключение,
которое генерируется, когда IP-адрес устройства недоступен.

При возникновении ошибки, на стандартный поток вывода должно выводиться сообщение исключения.

Для проверки измените IP-адрес на устройстве или в файле devices.yaml.

Задание 18.2
~~~~~~~~~~~~

Создать функцию send_config_commands

Функция подключается по SSH (с помощью netmiko) к одному устройству и выполняет перечень команд в конфигурационном режиме на основании переданных аргументов.

Параметры функции:

* device - словарь с параметрами подключения к устройству
* config_commands - список команд, которые надо выполнить

Функция возвращает строку с результатами выполнения команды:

.. code:: python

    In [7]: r1
    Out[7]:
    {'device_type': 'cisco_ios',
     'ip': '192.168.100.1',
     'username': 'cisco',
     'password': 'cisco',
     'secret': 'cisco'}

    In [8]: commands
    Out[8]: ['logging 10.255.255.1', 'logging buffered 20010', 'no logging console']

    In [9]: result = send_config_commands(r1, commands)

    In [10]: result
    Out[10]: 'config term\nEnter configuration commands, one per line.  End with CNTL/Z.\nR1(config)#logging 10.255.255.1\nR1(config)#logging buffered 20010\nR1(config)#no logging console\nR1(config)#end\nR1#'

    In [11]: print(result)
    config term
    Enter configuration commands, one per line.  End with CNTL/Z.
    R1(config)#logging 10.255.255.1
    R1(config)#logging buffered 20010
    R1(config)#no logging console
    R1(config)#end
    R1#


Скрипт должен отправлять команду command на все устройства из файла devices.yaml с помощью функции send_config_commands.

.. code:: python

    commands = [
        'logging 10.255.255.1', 'logging buffered 20010', 'no logging console'
    ]

Задание 18.2a
~~~~~~~~~~~~~

Скопировать функцию send_config_commands из задания 18.2 и добавить параметр log,
который контролирует будет ли выводится на стандартный поток вывода
информация о том к какому устройству выполняется подключение.

По умолчанию, результат должен выводиться.

Пример работы функции:

.. code:: python

    In [13]: result = send_config_commands(r1, commands)
    Подключаюсь к 192.168.100.1...

    In [14]: result = send_config_commands(r1, commands, log=False)

    In [15]:

Скрипт должен отправлять список команд commands на все устройства из файла devices.yaml с помощью функции send_config_commands.


Задание 18.2b
~~~~~~~~~~~~~

Скопировать функцию send_config_commands из задания 18.2a и добавить проверку на ошибки.

При выполнении каждой команды, скрипт должен проверять результат на такие ошибки:

* Invalid input detected
* Incomplete command
* Ambiguous command

Если при выполнении какой-то из команд возникла ошибка,
функция должна выводить сообщение на стандартный поток вывода с информацией
о том, какая ошибка возникла, при выполнении какой команды и на каком устройстве, например:
Команда "logging" выполнилась с ошибкой "Incomplete command." на устройстве 192.168.100.1

Ошибки должны выводиться всегда, независимо от значения параметра log.
При этом, log по-прежнему должен контролировать будет ли выводиться сообщение:
Подключаюсь к 192.168.100.1...


Функция send_config_commands теперь должна возвращать кортеж из двух словарей:

* первый словарь с выводом команд, которые выполнились без ошибки
* второй словарь с выводом команд, которые выполнились с ошибками

Оба словаря в формате (примеры словарей ниже):

* ключ - команда
* значение - вывод с выполнением команд

Проверить работу функции можно на одном устройстве.


Пример работы функции send_config_commands:

.. code:: python

    In [16]: commands
    Out[16]:
    ['logging 0255.255.1',
     'logging',
     'a',
     'logging buffered 20010',
     'ip http server']

    In [17]: result = send_config_commands(r1, commands)
    Подключаюсь к 192.168.100.1...
    Команда "logging 0255.255.1" выполнилась с ошибкой "Invalid input detected at '^' marker." на устройстве 192.168.100.1
    Команда "logging" выполнилась с ошибкой "Incomplete command." на устройстве 192.168.100.1
    Команда "a" выполнилась с ошибкой "Ambiguous command:  "a"" на устройстве 192.168.100.1

    In [18]: pprint(result, width=120)
    ({'ip http server': 'config term\n'
                        'Enter configuration commands, one per line.  End with CNTL/Z.\n'
                        'R1(config)#ip http server\n'
                        'R1(config)#',
      'logging buffered 20010': 'config term\n'
                                'Enter configuration commands, one per line.  End with CNTL/Z.\n'
                                'R1(config)#logging buffered 20010\n'
                                'R1(config)#'},
     {'a': 'config term\n'
           'Enter configuration commands, one per line.  End with CNTL/Z.\n'
           'R1(config)#a\n'
           '% Ambiguous command:  "a"\n'
           'R1(config)#',
      'logging': 'config term\n'
                 'Enter configuration commands, one per line.  End with CNTL/Z.\n'
                 'R1(config)#logging\n'
                 '% Incomplete command.\n'
                 '\n'
                 'R1(config)#',
      'logging 0255.255.1': 'config term\n'
                            'Enter configuration commands, one per line.  End with CNTL/Z.\n'
                            'R1(config)#logging 0255.255.1\n'
                            '                   ^\n'
                            "% Invalid input detected at '^' marker.\n"
                            '\n'
                            'R1(config)#'})

    In [19]: good, bad = result

    In [20]: good.keys()
    Out[20]: dict_keys(['logging buffered 20010', 'ip http server'])

    In [21]: bad.keys()
    Out[21]: dict_keys(['logging 0255.255.1', 'logging', 'a'])


Примеры команд с ошибками:

::

    R1(config)#logging 0255.255.1
                       ^
    % Invalid input detected at '^' marker.

    R1(config)#logging
    % Incomplete command.

    R1(config)#a
    % Ambiguous command:  "a"


Списки команд с ошибками и без:

.. code:: python

    commands_with_errors = ['logging 0255.255.1', 'logging', 'a']
    correct_commands = ['logging buffered 20010', 'ip http server']

    commands = commands_with_errors + correct_commands

Задание 18.2c
~~~~~~~~~~~~~

Скопировать функцию send_config_commands из задания 18.2b и переделать ее таким образом:
Если при выполнении команды возникла ошибка,
спросить пользователя надо ли выполнять остальные команды.

Варианты ответа [y]/n:

* y - выполнять остальные команды. Это значение по умолчанию, поэтому нажатие любой комбинации воспринимается как y
* n или no - не выполнять остальные команды

Функция send_config_commands по-прежнему должна возвращать кортеж из двух словарей:

* первый словарь с выводом команд, которые выполнились без ошибки
* второй словарь с выводом команд, которые выполнились с ошибками

Оба словаря в формате

* ключ - команда
* значение - вывод с выполнением команд

Проверить работу функции можно на одном устройстве.

Пример работы функции:

.. code:: python

    In [11]: result = send_config_commands(r1, commands)
    Подключаюсь к 192.168.100.1...
    Команда "logging 0255.255.1" выполнилась с ошибкой "Invalid input detected at '^' marker." на устройстве 192.168.100.1
    Продолжать выполнять команды? [y]/n: y
    Команда "logging" выполнилась с ошибкой "Incomplete command." на устройстве 192.168.100.1
    Продолжать выполнять команды? [y]/n: n

    In [12]: pprint(result)
    ({},
     {'logging': 'config term\n'
                 'Enter configuration commands, one per line.  End with CNTL/Z.\n'
                 'R1(config)#logging\n'
                 '% Incomplete command.\n'
                 '\n'
                 'R1(config)#',
      'logging 0255.255.1': 'config term\n'
                            'Enter configuration commands, one per line.  End with '
                            'CNTL/Z.\n'
                            'R1(config)#logging 0255.255.1\n'
                            '                   ^\n'
                            "% Invalid input detected at '^' marker.\n"
                            '\n'
                            'R1(config)#'})

Списки команд с ошибками и без:

.. code:: python

    commands_with_errors = ['logging 0255.255.1', 'logging', 'a']
    correct_commands = ['logging buffered 20010', 'ip http server']

    commands = commands_with_errors + correct_commands

Задание 18.3
~~~~~~~~~~~~

Создать функцию send_commands (для подключения по SSH используется netmiko).

Параметры функции:

* device - словарь с параметрами подключения к одному устройству
* show - одна команда show (строка)
* config - список с командами, которые надо выполнить в конфигурационном режиме

Аргументы show и config должны передаваться только как ключевые. При передачи
этих аргументов как позиционных, должно генерироваться исключение TypeError.

.. code:: python

    In [4]: send_commands(r1, 'sh clock')
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-4-75adcfb4a005> in <module>
    ----> 1 send_commands(r1, 'sh clock')

    TypeError: send_commands() takes 1 positional argument but 2 were given


В зависимости от того, какой аргумент был передан, функция вызывает разные функции
внутри. При вызове функции send_commands, всегда должен передаваться
только один из аргументов show, config. Если передаются оба аргумента, должно
генерироваться исключение ValueError.

Далее комбинация из аргумента и соответствующей функции:

* show - функция send_show_command из задания 18.1
* config - функция send_config_commands из задания 18.2

Функция возвращает строку с результатами выполнения команд или команды.

Проверить работу функции:

* со списком команд commands
* командой command

Пример работы функции:

.. code:: python

    In [14]: send_commands(r1, show='sh clock')
    Out[14]: '*17:06:12.278 UTC Wed Mar 13 2019'

    In [15]: commands = ['username user5 password pass5', 'username user6 password pass6']

    In [16]: send_commands(r1, config=commands)
    Out[16]: 'config term\nEnter configuration commands, one per line.  End with CNTL/Z.\nR1(config)#username user5 password pass5\nR1(config)#username user6 password pass6\nR1(config)#end\nR1#'


.. code:: python

    commands = ["logging 10.255.255.1", "logging buffered 20010", "no logging console"]
    command = "sh ip int br"


