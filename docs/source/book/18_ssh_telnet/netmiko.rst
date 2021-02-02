Модуль netmiko
--------------

Netmiko - это модуль, который позволяет упростить использование paramiko
для сетевых устройств. Netmiko использует paramiko, но при этом создает 
интерфейс и методы, которые нужны для работы с сетевым оборудованием.

Сначала netmiko нужно установить:

::

    pip install netmiko


Поддерживаемые типы устройств
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

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
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

В словаре могут указываться такие параметры:

.. code:: python

    cisco_router = {
        'device_type': 'cisco_ios',
        'host': '192.168.1.1',
        'username': 'user',
        'password': 'userpass',
        'secret': 'enablepass',
        'port': 20022,
    }

Подключение по SSH
~~~~~~~~~~~~~~~~~~

.. code:: python

    ssh = ConnectHandler(**cisco_router)

Режим enable
~~~~~~~~~~~~

Перейти в режим enable:

.. code:: python

    ssh.enable()

Выйти из режима enable:

.. code:: python

    ssh.exit_enable_mode()

Отправка команд
~~~~~~~~~~~~~~~

В netmiko есть несколько способов отправки команд: 

* ``send_command`` - отправить одну команду 
* ``send_config_set`` - отправить список команд или команду в конфигурационном режиме 
* ``send_config_from_file`` - отправить команды из файла (использует внутри метод ``send_config_set``)
* ``send_command_timing`` - отправить команду и подождать вывод на основании таймера

``send_command``
^^^^^^^^^^^^^^^^

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
^^^^^^^^^^^^^^^^^^^^^^^^^

Метод ``send_config_from_file`` отправляет команды из указанного файла в
конфигурационный режим.

Пример использования:

.. code:: python

    result = ssh.send_config_from_file('config_ospf.txt')

Метод открывает файл, считывает команды и передает их методу
``send_config_set``.

Дополнительные методы
~~~~~~~~~~~~~~~~~~~~~

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

    device = {
        "device_type": "cisco_ios_telnet",
        "host": "192.168.100.1",
        "username": "cisco",
        "password": "cisco",
        "secret": "cisco",
    }

В остальном, методы, которые применимы к SSH, применимы и к Telnet.
Пример, аналогичный примеру с SSH (файл 4_netmiko_telnet.py):

.. code:: python

    from pprint import pprint
    import yaml
    from netmiko import (
        ConnectHandler,
        NetmikoTimeoutException,
        NetmikoAuthenticationException,
    )


    def send_show_command(device, commands):
        result = {}
        try:
            with ConnectHandler(**device) as ssh:
                ssh.enable()
                for command in commands:
                    output = ssh.send_command(command)
                    result[command] = output
            return result
        except (NetmikoTimeoutException, NetmikoAuthenticationException) as error:
            print(error)


    if __name__ == "__main__":
        device = {
            "device_type": "cisco_ios_telnet",
            "host": "192.168.100.1",
            "username": "cisco",
            "password": "cisco",
            "secret": "cisco",
        }
        result = send_show_command(device, ["sh clock", "sh ip int br"])
        pprint(result, width=120)



Аналогично работают и методы: 

* ``send_command_timing()`` 
* ``find_prompt()`` 
* ``send_config_set()`` 
* ``send_config_from_file()`` 
* ``check_enable_mode()`` 
* ``disconnect()``


Пример использования netmiko
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Пример использования netmiko (файл 4_netmiko.py):

.. code:: python

    from pprint import pprint
    import yaml
    from netmiko import (
        ConnectHandler,
        NetmikoTimeoutException,
        NetmikoAuthenticationException,
    )


    def send_show_command(device, commands):
        result = {}
        try:
            with ConnectHandler(**device) as ssh:
                ssh.enable()
                for command in commands:
                    output = ssh.send_command(command)
                    result[command] = output
            return result
        except (NetmikoTimeoutException, NetmikoAuthenticationException) as error:
            print(error)


    if __name__ == "__main__":
        with open("devices.yaml") as f:
            devices = yaml.safe_load(f)
        for device in devices:
            result = send_show_command(device, ["sh clock", "sh ip int br"])
            pprint(result, width=120)



В этом примере не передается команда terminal length, так как netmiko по
умолчанию выполняет эту команду.

Результат выполнения скрипта:

::

    {'sh clock': '*09:12:15.210 UTC Mon Jul 20 2020',
     'sh ip int br': 'Interface     IP-Address      OK? Method Status                Protocol\n'
                     'Ethernet0/0   192.168.100.1   YES NVRAM  up                    up      \n'
                     'Ethernet0/1   192.168.200.1   YES NVRAM  up                    up      \n'
                     'Ethernet0/2   unassigned      YES NVRAM  up                    up      \n'
                     'Ethernet0/3   192.168.130.1   YES NVRAM  up                    up      \n'}
    {'sh clock': '*09:12:24.507 UTC Mon Jul 20 2020',
     'sh ip int br': 'Interface     IP-Address      OK? Method Status                Protocol\n'
                     'Ethernet0/0   192.168.100.2   YES NVRAM  up                    up      \n'
                     'Ethernet0/1   unassigned      YES NVRAM  up                    up      \n'
                     'Ethernet0/2   unassigned      YES NVRAM  administratively down down    \n'
                     'Ethernet0/3   unassigned      YES NVRAM  administratively down down    \n'}
    {'sh clock': '*09:12:33.573 UTC Mon Jul 20 2020',
     'sh ip int br': 'Interface     IP-Address      OK? Method Status                Protocol\n'
                     'Ethernet0/0   192.168.100.3   YES NVRAM  up                    up      \n'
                     'Ethernet0/1   unassigned      YES NVRAM  up                    up      \n'
                     'Ethernet0/2   unassigned      YES NVRAM  administratively down down    \n'
                     'Ethernet0/3   unassigned      YES NVRAM  administratively down down    \n'}


Постраничный вывод команд
~~~~~~~~~~~~~~~~~~~~~~~~~

Пример использования paramiko для работы с постраничным выводом команд
show (файл 4_netmiko_more.py):

.. code:: python

    from netmiko import ConnectHandler, NetmikoTimeoutException
    import yaml


    def send_show_command(device_params, command):
        with ConnectHandler(**device_params) as ssh:
            ssh.enable()
            prompt = ssh.find_prompt()
            ssh.send_command("terminal length 100")
            ssh.write_channel(f"{command}\n")
            output = ""
            while True:
                try:
                    page = ssh.read_until_pattern(f"More|{prompt}")
                    output += page
                    if "More" in page:
                        ssh.write_channel(" ")
                    elif prompt in output:
                        break
                except NetmikoTimeoutException:
                    break
        return output


    if __name__ == "__main__":
        with open("devices.yaml") as f:
            devices = yaml.safe_load(f)
        print(send_show_command(devices[0], "sh run"))

