Модуль netmiko
--------------

Netmiko - это модуль, который позволяет упростить использование paramiko
для сетевых устройств. Netmiko использует paramiko, но при этом создает 
интерфейс и методы, которые нужны для работы с сетевым оборудованием.

Сначала netmiko нужно установить:

::

    pip install netmiko

Пример использования netmiko (файл 4_netmiko.py):

.. literalinclude:: /pyneng-examples-exercises/examples/19_ssh_telnet/4_netmiko.py
  :language: python
  :linenos:

Пример, который использовался с pexpect, telnetlib и paramiko выглядит значительно
проще с netmiko. 

Разберемся с содержимым скрипта: 

* device_params - это словарь, в котором указываются параметры устройства. 
  Параметр device_type - это предопределенные значения, которые понимает netmiko. 
  В данном случае, так как подключение выполняется к устройству с Cisco IOS, 
  используется значение ``cisco_ios``
* ``with ConnectHandler(**device_params) as ssh`` - устанавливается соединение 
  с устройством на основе параметров, которые находятся в словаре 

  * две звездочки перед словарем - это распаковка словаря (подробнее в разделе 
    `Распаковка аргументов <https://pyneng.readthedocs.io/ru/latest/book/09_functions/3b_func_unpacking_args.html>`__) 

* ``ssh.enable()`` - переход в режим enable. Пароль передается автоматически - 
  используется значение ключа secret, который указан в словаре device_params 
* ``result = ssh.send_command(COMMAND)`` - отправка команды и получение вывода

В этом примере не передается команда terminal length, так как netmiko по
умолчанию выполняет эту команду.

Так выглядит результат выполнения скрипта:

::

    $ python 4_netmiko.py "sh ip int br"
    Username: cisco
    Password:
    Enter enable password:
    Connection to device 192.168.100.1
    Interface              IP-Address      OK? Method Status                Protocol
    FastEthernet0/0        192.168.100.1   YES NVRAM  up                    up
    FastEthernet0/1        unassigned      YES NVRAM  up                    up
    FastEthernet0/1.10     10.1.10.1       YES manual up                    up
    FastEthernet0/1.20     10.1.20.1       YES manual up                    up
    FastEthernet0/1.30     10.1.30.1       YES manual up                    up
    FastEthernet0/1.40     10.1.40.1       YES manual up                    up
    FastEthernet0/1.50     10.1.50.1       YES manual up                    up
    FastEthernet0/1.60     10.1.60.1       YES manual up                    up
    FastEthernet0/1.70     10.1.70.1       YES manual up                    up
    Connection to device 192.168.100.2
    Interface              IP-Address      OK? Method Status                Protocol
    FastEthernet0/0        192.168.100.2   YES NVRAM  up                    up
    FastEthernet0/1        unassigned      YES NVRAM  up                    up
    FastEthernet0/1.10     10.2.10.1       YES manual up                    up
    FastEthernet0/1.20     10.2.20.1       YES manual up                    up
    FastEthernet0/1.30     10.2.30.1       YES manual up                    up
    FastEthernet0/1.40     10.2.40.1       YES manual up                    up
    FastEthernet0/1.50     10.2.50.1       YES manual up                    up
    FastEthernet0/1.60     10.2.60.1       YES manual up                    up
    FastEthernet0/1.70     10.2.70.1       YES manual up                    up
    Connection to device 192.168.100.3
    Interface              IP-Address      OK? Method Status                Protocol
    FastEthernet0/0        192.168.100.3   YES NVRAM  up                    up
    FastEthernet0/1        unassigned      YES NVRAM  up                    up
    FastEthernet0/1.10     10.3.10.1       YES manual up                    up
    FastEthernet0/1.20     10.3.20.1       YES manual up                    up
    FastEthernet0/1.30     10.3.30.1       YES manual up                    up
    FastEthernet0/1.40     10.3.40.1       YES manual up                    up
    FastEthernet0/1.50     10.3.50.1       YES manual up                    up
    FastEthernet0/1.60     10.3.60.1       YES manual up                    up
    FastEthernet0/1.70     10.3.70.1       YES manual up                    up

В выводе нет никаких лишних приглашений, только вывод команды sh ip int
br.

Возможности netmiko
~~~~~~~~~~~~~~~~~~~

Так как netmiko наиболее удобный модуль для подключения к сетевому
оборудованию, разберемся с ним подробней.

Поддерживаемые типы устройств
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Netmiko поддерживает несколько типов устройств: 

* Arista vEOS 
* Cisco ASA 
* Cisco IOS 
* Cisco IOS-XR 
* Cisco SG300 
* HP Comware7 
* HP ProCurve 
* Juniper Junos 
* Linux 
* и другие

Актуальный список можно посмотреть в
`репозитории <https://github.com/ktbyers/netmiko>`__ модуля.

Словарь, определяющий параметры устройств
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

В словаре могут указываться такие параметры:

.. code:: python

    cisco_router = {'device_type': 'cisco_ios', # предопределенный тип устройства
                    'ip': '192.168.1.1', # адрес устройства
                    'username': 'user', # имя пользователя
                    'password': 'userpass', # пароль пользователя
                    'secret': 'enablepass', # пароль режима enable
                    'port': 20022, # порт SSH, по умолчанию 22
                     }

Подключение по SSH
^^^^^^^^^^^^^^^^^^

.. code:: python

    ssh = ConnectHandler(**cisco_router)

Режим enable
^^^^^^^^^^^^

Перейти в режим enable:

.. code:: python

    ssh.enable()

Выйти из режима enable:

.. code:: python

    ssh.exit_enable_mode()

Отправка команд
^^^^^^^^^^^^^^^

В netmiko есть несколько способов отправки команд: 

* ``send_command`` - отправить одну команду 
* ``send_config_set`` - отправить список команд или команду в конфигурационном режиме 
* ``send_config_from_file`` - отправить команды из файла (использует внутри метод ``send_config_set``)
* ``send_command_timing`` - отправить команду и подождать вывод на основании таймера

``send_command``
****************

Метод send_command позволяет отправить одну команду на устройство.

Например:

.. code:: python

    result = ssh.send_command('show ip int br')

Метод работает таким образом: 

* отправляет команду на устройство и получает вывод до строки 
  с приглашением или до указанной строки 

  * приглашение определяется автоматически 
  * если на вашем устройстве оно не определилось, можно просто указать строку, до которой считывать вывод
  * ранее так работал метод ``send_command_expect``, но с версии 1.0.0
    так работает ``send_command``, а метод ``send_command_expect`` оставлен для совместимости 

* метод возвращает вывод команды 
* методу можно передавать такие параметры: 

  * ``command_string`` - команда 
  * ``expect_string`` - до какой строки считывать вывод 
  * ``delay_factor`` - параметр позволяет увеличить задержку до начала поиска строки 
  * ``max_loops`` - количество итераций, до того как метод выдаст ошибку 
    (исключение). По умолчанию 500 
  * ``strip_prompt`` - удалить приглашение из вывода. По умолчанию удаляется 
  * ``strip_command`` - удалить саму команду из вывода

В большинстве случаев достаточно будет указать только команду.

``send_config_set``
*******************

Метод ``send_config_set`` позволяет отправить команду или несколько
команд конфигурационного режима.

Пример использования:

.. code:: python

    commands = ['router ospf 1',
                'network 10.0.0.0 0.255.255.255 area 0',
                'network 192.168.100.0 0.0.0.255 area 1']

    result = ssh.send_config_set(commands)

Метод работает таким образом: 

* заходит в конфигурационный режим, 
* затем передает все команды 
* и выходит из конфигурационного режима 
* в зависимости от типа устройства, выхода из конфигурационного режима может
  и не быть. Например, для IOS-XR выхода не будет, так как сначала надо
  закоммитить изменения

``send_config_from_file``
*************************

Метод ``send_config_from_file`` отправляет команды из указанного файла в
конфигурационный режим.

Пример использования:

.. code:: python

    result = ssh.send_config_from_file('config_ospf.txt')

Метод открывает файл, считывает команды и передает их методу
``send_config_set``.

Дополнительные методы
^^^^^^^^^^^^^^^^^^^^^

Кроме перечисленных методов для отправки команд, netmiko поддерживает
такие методы: 

* ``config_mode`` - перейти в режим конфигурации: ``ssh.config_mode()`` 
* ``exit_config_mode`` - выйти из режима конфигурации: ``ssh.exit_config_mode()`` 
* ``check_config_mode`` - проверить, находится ли netmiko в режиме конфигурации (возвращает True,
  если в режиме конфигурации, и False - если нет): ``ssh.check_config_mode()`` 
* ``find_prompt`` - возвращает текущее приглашение устройства: ``ssh.find_prompt()`` 
* ``commit`` - выполнить commit на IOS-XR и Juniper: ``ssh.commit()`` 
* ``disconnect`` - завершить соединение SSH

.. note::

    Выше ssh - это созданное предварительно соединение SSH:
    ``ssh = ConnectHandler(**cisco_router)``

Поддержка Telnet
~~~~~~~~~~~~~~~~

С версии 1.0.0 netmiko поддерживает подключения по Telnet, пока что
только для Cisco IOS устройств.

Внутри netmiko использует telnetlib для подключения по Telnet. Но, при
этом, предоставляет тот же интерфейс для работы, что и подключение по
SSH.

Для того, чтобы подключиться по Telnet, достаточно в словаре, который
определяет параметры подключения, указать тип устройства
'cisco_ios_telnet':

.. code:: python

    DEVICE_PARAMS = {'device_type': 'cisco_ios_telnet',
                     'ip': IP,
                     'username':USER,
                     'password':PASSWORD,
                     'secret':ENABLE_PASS }

В остальном, методы, которые применимы к SSH, применимы и к Telnet.
Пример, аналогичный примеру с SSH (файл 4_netmiko_telnet.py):

.. literalinclude:: /pyneng-examples-exercises/examples/19_ssh_telnet/4_netmiko_telnet.py
  :language: python
  :emphasize-lines: 17
  :linenos:

Аналогично работают и методы: 

* ``send_command_timing()`` 
* ``find_prompt()`` 
* ``send_config_set()`` 
* ``send_config_from_file()`` 
* ``check_enable_mode()`` 
* ``disconnect()``
