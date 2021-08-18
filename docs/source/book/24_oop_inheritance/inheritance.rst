Основы наследования
~~~~~~~~~~~~~~~~~~~

Наследование позволяет создавать новые классы на основе существующих.
Различают дочерний и родительские классы: дочерний класс наследует родительский.
При наследовании, дочерний класс наследует все методы и атрибуты родительского класса.

Пример класса ConnectSSH, который выполняет подключение по SSH с помощью paramiko:

.. code:: python

    import paramiko
    import time


    class ConnectSSH:
        def __init__(self, ip, username, password):
            self.ip = ip
            self.username = username
            self.password = password
            self._MAX_READ = 10000

            client = paramiko.SSHClient()
            client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

            client.connect(
                hostname=ip,
                username=username,
                password=password,
                look_for_keys=False,
                allow_agent=False)

            self._ssh = client.invoke_shell()
            time.sleep(1)
            self._ssh.recv(self._MAX_READ)

        def __enter__(self):
            return self

        def __exit__(self, exc_type, exc_value, traceback):
            self._ssh.close()

        def close(self):
            self._ssh.close()

        def send_show_command(self, command):
            self._ssh.send(command + '\n')
            time.sleep(2)
            result = self._ssh.recv(self._MAX_READ).decode('ascii')
            return result

        def send_config_commands(self, commands):
            if isinstance(commands, str):
                commands = [commands]
            for command in commands:
                self._ssh.send(command + '\n')
                time.sleep(0.5)
            result = self._ssh.recv(self._MAX_READ).decode('ascii')
            return result

Этот класс будет использоваться как основа для классов, которые отвечают за подключение
к устройствам разных вендоров. Например, класс CiscoSSH будет отвечать за подключение 
к устройствам Cisco будет наследовать класс ConnectSSH.

Синтаксис наследования:

.. code:: python

    class CiscoSSH(ConnectSSH):
        pass

После этого в классе CiscoSSH доступны все методы и атрибуты класса ConnectSSH:

.. code:: python

    In [3]: r1 = CiscoSSH('192.168.100.1', 'cisco', 'cisco')

    In [4]: r1.ip
    Out[4]: '192.168.100.1'

    In [5]: r1._MAX_READ
    Out[5]: 10000

    In [6]: r1.send_show_command('sh ip int br')
    Out[6]: 'sh ip int br\r\nInterface                  IP-Address      OK? Method Status                Protocol\r\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \r\nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \r\nEthernet0/2                19.1.1.1        YES NVRAM  up                    up      \r\nEthernet0/3                192.168.230.1   YES NVRAM  up                    up      \r\nLoopback0                  4.4.4.4         YES NVRAM  up                    up      \r\nLoopback33                 3.3.3.3         YES manual up                    up      \r\nLoopback90                 90.1.1.1        YES manual up                    up      \r\nR1#'



    In [7]: r1.send_show_command('enable')
    Out[7]: 'enable\r\nPassword: '

    In [8]: r1.send_show_command('cisco')
    Out[8]: '\r\nR1#'

    In [9]: r1.send_config_commands(['conf t', 'int loopback 33',
       ...:                          'ip address 3.3.3.3 255.255.255.255', 'end'])
    Out[9]: 'conf t\r\nEnter configuration commands, one per line.  End with CNTL/Z.\r\nR1(config)#int loopback 33\r\nR1(config-if)#ip address 3.3.3.3 255.255.255.255\r\nR1(config-if)#end\r\nR1#'


После наследования всех методов родительского класса, дочерний класс может:

* оставить их без изменения
* полностью переписать их
* дополнить метод
* добавить свои методы

В классе CiscoSSH надо создать метод __init__ и добавить к нему параметры:

* enable_password - пароль enable
* disable_paging - отвечает за включение/отключение постраничного вывода команд

Метод __init__ можно создать полностью с нуля, однако базовая логика подключения по SSH
будет одинаковая в ConnectSSH и CiscoSSH, поэтому лучше добавить необходимые параметры,
а для подключения, вызвать метод __init__ у класса ConnectSSH.
Есть несколько вариантов вызова родительского метода, например, все эти варианты вызовут
метод send_show_command родительского класса из дочернего класса CiscoSSH:

.. code:: python

    command_result = ConnectSSH.send_show_command(self, command)
    command_result = super(CiscoSSH, self).send_show_command(command)
    command_result = super().send_show_command(command)

Первый вариант ``ConnectSSH.send_show_command`` явно указывает имя родительского 
класса - это самый понятный вариант для восприятия, однако его минус в том, что 
при смене имени родительского класса, имя надо будет менять во всех местах, где
вызывались методы родительского класса. Также у этого варианта есть минусы, при
использовании множественного наследования.
Второй и третий вариант по сути равнозначны, но третий короче, поэтому мы будем
использовать его.

Класс CiscoSSH с методом __init__:

.. code:: python

    class CiscoSSH(ConnectSSH):
        def __init__(self, ip, username, password, enable_password,
                     disable_paging=True):
            super().__init__(ip, username, password)
            self._ssh.send('enable\n')
            self._ssh.send(enable_password + '\n')
            if disable_paging:
                self._ssh.send('terminal length 0\n')
            time.sleep(1)
            self._ssh.recv(self._MAX_READ)

Метод __init__ в классе CiscoSSH добавил параметры enable_password и disable_paging,
и использует их соответственно для перехода в режим enable и отключения постраничного вывода.
Пример подключения:

.. code:: python

    In [10]: r1 = CiscoSSH('192.168.100.1', 'cisco', 'cisco', 'cisco')

    In [11]: r1.send_show_command('sh clock')
    Out[11]: 'sh clock\r\n*11:30:50.280 UTC Mon Aug 5 2019\r\nR1#'

Теперь при подключении также выполняется переход в режим enable и по умолчанию отключен
paging, так что можно попробовать выполнить длинную команду, например sh run.

Еще один метод, который стоит доработать - метод send_config_commands: так как 
класс CiscoSSH предназначен для работы с Cisco, можно добавить в него переход 
в конфигурационный режим перед командами и выход после.

.. code:: python

    class CiscoSSH(ConnectSSH):
        def __init__(self, ip, username, password, enable_password,
                     disable_paging=True):
            super().__init__(ip, username, password)
            self._ssh.send('enable\n')
            self._ssh.send(enable_password + '\n')
            if disable_paging:
                self._ssh.send('terminal length 0\n')
            time.sleep(1)
            self._ssh.recv(self._MAX_READ)

        def config_mode(self):
            self._ssh.send('conf t\n')
            time.sleep(0.5)
            result = self._ssh.recv(self._MAX_READ).decode('ascii')
            return result

        def exit_config_mode(self):
            self._ssh.send('end\n')
            time.sleep(0.5)
            result = self._ssh.recv(self._MAX_READ).decode('ascii')
            return result

        def send_config_commands(self, commands):
            result = self.config_mode()
            result += super().send_config_commands(commands)
            result += self.exit_config_mode()
            return result

Пример использования метода send_config_commands:

.. code:: python

    In [12]: r1 = CiscoSSH('192.168.100.1', 'cisco', 'cisco', 'cisco')

    In [13]: r1.send_config_commands(['interface loopback 33',
        ...:                          'ip address 3.3.3.3 255.255.255.255'])
    Out[13]: 'conf t\r\nEnter configuration commands, one per line.  End with CNTL/Z.\r\nR1(config)#interface loopback 33\r\nR1(config-if)#ip address 3.3.3.3 255.255.255.255\r\nR1(config-if)#end\r\nR1#'

