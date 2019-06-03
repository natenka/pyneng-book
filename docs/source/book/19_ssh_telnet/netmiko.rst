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


.. toctree::
   :maxdepth: 1

   netmiko_details
