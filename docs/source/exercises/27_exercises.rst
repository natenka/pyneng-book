.. raw:: latex

   \newpage

Задания
=======

.. include:: ./pytest.rst


Задание 27.1
~~~~~~~~~~~~

Создать класс CiscoSSH, который наследует класс BaseSSH из файла base_connect_class.py.

Создать метод __init__ в классе CiscoSSH таким образом, чтобы после подключения по SSH выполнялся переход в режим enable.

Для этого в методе __init__ должен сначала вызываться метод __init__ класса ConnectSSH, а затем выполняться переход в режим enable.

.. code-block:: python

    In [2]: from task_27_1 import CiscoSSH

    In [3]: r1 = CiscoSSH(**device_params)

    In [4]: r1.send_show_command('sh ip int br')
    Out[4]: 'Interface                  IP-Address      OK? Method Status                Protocol\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \nEthernet0/2                190.16.200.1    YES NVRAM  up                    up      \nEthernet0/3                192.168.230.1   YES NVRAM  up                    up      \nEthernet0/3.100            10.100.0.1      YES NVRAM  up                    up      \nEthernet0/3.200            10.200.0.1      YES NVRAM  up                    up      \nEthernet0/3.300            10.30.0.1       YES NVRAM  up                    up      '


Задание 27.1a
~~~~~~~~~~~~~

Дополнить класс CiscoSSH из задания 27.1.

Перед подключением по SSH необходимо проверить если ли в словаре
с параметрами подключения такие параметры: username, password, secret.
Если нет, запросить их у пользователя, а затем выполнять подключение.

.. code-block:: python

    In [1]: from task_27_1a import CiscoSSH

    In [2]: device_params = {
       ...:         'device_type': 'cisco_ios',
       ...:         'ip': '192.168.100.1',
       ...: }

    In [3]: r1 = CiscoSSH(**device_params)
    Введите имя пользователя: cisco
    Введите пароль:
    Введите пароль для режима enable:

    In [4]: r1.send_show_command('sh ip int br')
    Out[4]: 'Interface                  IP-Address      OK? Method Status                Protocol\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \nEthernet0/2                190.16.200.1    YES NVRAM  up                    up      \nEthernet0/3                192.168.230.1   YES NVRAM  up                    up      \nEthernet0/3.100            10.100.0.1      YES NVRAM  up                    up      \nEthernet0/3.200            10.200.0.1      YES NVRAM  up                    up      \nEthernet0/3.300            10.30.0.1       YES NVRAM  up                    up      '

Задание 27.2
~~~~~~~~~~~~

Создать класс MyNetmiko, который наследует класс CiscoIosBase из netmiko.

Переписать метод __init__ в классе MyNetmiko таким образом, чтобы после подключения по SSH выполнялся переход в режим enable.

Для этого в методе __init__ должен сначала вызываться метод __init__ класса CiscoIosBase, а затем выполнялся переход в режим enable.

Проверить, что в классе MyNetmiko доступны методы send_command и send_config_set

.. code-block:: python

    In [2]: from task_27_2 import MyNetmiko

    In [3]: r1 = MyNetmiko(**device_params)

    In [4]: r1.send_command('sh ip int br')
    Out[4]: 'Interface                  IP-Address      OK? Method Status                Protocol\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \nEthernet0/2                190.16.200.1    YES NVRAM  up                    up      \nEthernet0/3                192.168.230.1   YES NVRAM  up                    up      \nEthernet0/3.100            10.100.0.1      YES NVRAM  up                    up      \nEthernet0/3.200            10.200.0.1      YES NVRAM  up                    up      \nEthernet0/3.300            10.30.0.1       YES NVRAM  up                    up      '

Задание 27.2a
~~~~~~~~~~~~~

Дополнить класс MyNetmiko из задания 27.2.

Добавить метод _check_error_in_command, который выполняет проверку на такие ошибки:

 * Invalid input detected, Incomplete command, Ambiguous command

Метод ожидает как аргумент команду и вывод команды.
Если в выводе не обнаружена ошибка, метод ничего не возвращает.
Если в выводе найдена ошибка, метод генерирует исключение ErrorInCommand с сообщениеем о том какая ошибка была обнаружена, на каком устройстве и в какой команде.

Переписать метод send_command netmiko, добавив в него проверку на ошибки.

.. code-block:: python

    In [2]: from task_27_2a import MyNetmiko

    In [3]: r1 = MyNetmiko(**device_params)

    In [4]: r1.send_command('sh ip int br')
    Out[4]: 'Interface                  IP-Address      OK? Method Status                Protocol\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \nEthernet0/2                190.16.200.1    YES NVRAM  up                    up      \nEthernet0/3                192.168.230.1   YES NVRAM  up                    up      \nEthernet0/3.100            10.100.0.1      YES NVRAM  up                    up      \nEthernet0/3.200            10.200.0.1      YES NVRAM  up                    up      \nEthernet0/3.300            10.30.0.1       YES NVRAM  up                    up      '

    In [5]: r1.send_command('sh ip br')
    ---------------------------------------------------------------------------
    ErrorInCommand                            Traceback (most recent call last)
    <ipython-input-2-1c60b31812fd> in <module>()
    ----> 1 r1.send_command('sh ip br')
    ...
    ErrorInCommand: При выполнении команды "sh ip br" на устройстве 192.168.100.1 возникла ошибка "Invalid input detected at '^' marker."

Задание 27.2b
~~~~~~~~~~~~~

Дополнить класс MyNetmiko из задания 27.2a.

Переписать метод send_config_set netmiko, добавив в него проверку на ошибки с помощью метода _check_error_in_command.

Метод send_config_set должен отправлять команды по одной и проверять каждую на ошибки.
Если при выполнении команд не обнаружены ошибки, метод send_config_set возвращает вывод команд.

.. code-block:: python

    In [2]: from task_27_2b import MyNetmiko

    In [3]: r1 = MyNetmiko(**device_params)

    In [4]: r1.send_config_set('lo')
    ---------------------------------------------------------------------------
    ErrorInCommand                            Traceback (most recent call last)
    <ipython-input-2-8e491f78b235> in <module>()
    ----> 1 r1.send_config_set('lo')
    ...
    ErrorInCommand: При выполнении команды "lo" на устройстве 192.168.100.1 возникла ошибка "Incomplete command."

Задание 27.2c
~~~~~~~~~~~~~


Проверить, что метод send_command класса MyNetmiko из задания 27.2b, принимает дополнительные аргументы (как в netmiko), кроме команды.

Если возникает ошибка, переделать метод таким образом, чтобы он принимал любые аргументы, которые поддерживает netmiko.

.. code-block:: python

    In [2]: from task_27_2c import MyNetmiko

    In [3]: r1 = MyNetmiko(**device_params)

    In [4]: r1.send_command('sh ip int br', strip_command=False)
    Out[4]: 'sh ip int br\nInterface                  IP-Address      OK? Method Status                Protocol\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \nEthernet0/2                190.16.200.1    YES NVRAM  up                    up      \nEthernet0/3                192.168.230.1   YES NVRAM  up                    up      \nEthernet0/3.100            10.100.0.1      YES NVRAM  up                    up      \nEthernet0/3.200            10.200.0.1      YES NVRAM  up                    up      \nEthernet0/3.300            10.30.0.1       YES NVRAM  up                    up      '

    In [5]: r1.send_command('sh ip int br', strip_command=True)
    Out[5]: 'Interface                  IP-Address      OK? Method Status                Protocol\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \nEthernet0/2                190.16.200.1    YES NVRAM  up                    up      \nEthernet0/3                192.168.230.1   YES NVRAM  up                    up      \nEthernet0/3.100            10.100.0.1      YES NVRAM  up                    up      \nEthernet0/3.200            10.200.0.1      YES NVRAM  up                    up      \nEthernet0/3.300            10.30.0.1       YES NVRAM  up                    up      '

Задание 27.2d
~~~~~~~~~~~~~

Дополнить класс MyNetmiko из задания 27.2c или задания 27.2b.

Добавить параметр ignore_errors в метод send_config_set.
Если передано истинное значение, не надо выполнять проверку на ошибки и метод должен работать точно так же как метод send_config_set в netmiko.

Если значение ложное, ошибки должны проверяться.

По умолчанию ошибки должны игнорироваться.

.. code-block:: python

    In [2]: from task_27_2d import MyNetmiko

    In [3]: r1 = MyNetmiko(**device_params)

    In [6]: r1.send_config_set('lo')
    Out[6]: 'config term\nEnter configuration commands, one per line.  End with CNTL/Z.\nR1(config)#lo\n% Incomplete command.\n\nR1(config)#end\nR1#'

    In [7]: r1.send_config_set('lo', ignore_errors=True)
    Out[7]: 'config term\nEnter configuration commands, one per line.  End with CNTL/Z.\nR1(config)#lo\n% Incomplete command.\n\nR1(config)#end\nR1#'

    In [8]: r1.send_config_set('lo', ignore_errors=False)
    ---------------------------------------------------------------------------
    ErrorInCommand                            Traceback (most recent call last)
    <ipython-input-8-704f2e8d1886> in <module>()
    ----> 1 r1.send_config_set('lo', ignore_errors=False)

    ...
    ErrorInCommand: При выполнении команды "lo" на устройстве 192.168.100.1 возникла ошибка "Incomplete command."

