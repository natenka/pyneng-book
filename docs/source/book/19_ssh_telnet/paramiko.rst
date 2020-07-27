Модуль paramiko
---------------

Paramiko - это реализация протокола SSHv2 на Python. Paramiko
предоставляет функциональность клиента и сервера. В книге рассматривается
только функциональность клиента.

Так как Paramiko не входит в стандартную библиотеку модулей Python, его
нужно установить:

::

    pip install paramiko


Подключение выполняется таким образом: сначала создается клиент и выполняются настройки клиента,
затем выполняется подключение и получение интерактивной сессии:

.. code:: python

    In [2]: client = paramiko.SSHClient()

    In [3]: client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

    In [4]: client.connect(hostname="192.168.100.1", username="cisco", password="cisco",
       ...: look_for_keys=False, allow_agent=False)

    In [5]: ssh = client.invoke_shell()

SSHClient это класс, который представляет соединение к SSH-серверу. Он выполняет аутентификацию клиента. 
Следующая настройка ``set_missing_host_key_policy`` не является обязательной, она указывает 
какую политику использовать, когда выполнятся подключение к серверу, ключ которого неизвестен. 
Политика ``paramiko.AutoAddPolicy()`` автоматически добавляет новое имя хоста и ключ в локальный
объект HostKeys. 

Метод ``connect`` выполняет подключение к SSH-серверу и аутентифицирует подключение. Параметры:

* ``look_for_keys`` - по умолчанию paramiko выполняет аутентификацию по
  ключам. Чтобы отключить это, надо поставить флаг в False 
* ``allow_agent`` - paramiko может подключаться к локальному SSH агенту 
  ОС. Это нужно при работе с ключами, а так как в данном случае 
  аутентификация выполняется по логину/паролю, это нужно отключить. 

После выполнения предыдущей команды уже есть подключение к серверу. Метод ``invoke_shell`` позволяет
установить интерактивную сессию SSH с сервером.

Метод send
~~~~~~~~~~

Метод ``send`` - отправляет указанную строку в сессию и возвращает количество отправленных байт
или ноль если сессия закрыта и не удалось отправить команду:

.. code:: python

    In [7]: ssh.send("enable\n")
    Out[7]: 7

    In [8]: ssh.send("cisco\n")
    Out[8]: 6

    In [9]: ssh.send("sh ip int br\n")
    Out[9]: 13


.. warning::

    В коде после send надо будет ставить time.sleep, особенно между send и recv.
    Так как это интерактивная сессия и команды набираются медленно, все работает и без пауз.

Метод recv
~~~~~~~~~~

Метод ``recv`` получает данные из сессии. В скобках  указывается максимальное значение в байтах,
которое нужно получить. Этот метод возвращает считанную строку.

.. code:: python

    In [10]: ssh.recv(3000)
    Out[10]: b'\r\nR1>enable\r\nPassword: \r\nR1#sh ip int br\r\nInterface                  IP-Address      OK? Method Status                Protocol\r\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \r\nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \r\nEthernet0/2                unassigned      YES NVRAM  up                    up      \r\nEthernet0/3                192.168.130.1   YES NVRAM  up                    up      \r\nLoopback22                 10.2.2.2        YES manual up                    up      \r\nLoopback33                 unassigned      YES unset  up                    up      \r\nLoopback45                 unassigned      YES unset  up                    up      \r\nLoopback55                 5.5.5.5         YES manual up                    up      \r\nR1#'

Метод close
~~~~~~~~~~~

Метод close закрывает сессию:

.. code:: python

    In [11]: ssh.close()


Пример использования paramiko
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Пример использования paramiko (файл 3_paramiko.py):

.. code:: python

    import paramiko
    import time
    import socket
    from pprint import pprint


    def send_show_command(
        ip,
        username,
        password,
        enable,
        command,
        max_bytes=60000,
        short_pause=1,
        long_pause=5,
    ):
        cl = paramiko.SSHClient()
        cl.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        cl.connect(
            hostname=ip,
            username=username,
            password=password,
            look_for_keys=False,
            allow_agent=False,
        )
        with cl.invoke_shell() as ssh:
            ssh.send("enable\n")
            ssh.send(f"{enable}\n")
            time.sleep(short_pause)
            ssh.send("terminal length 0\n")
            time.sleep(short_pause)
            ssh.recv(max_bytes)

            result = {}
            for command in commands:
                ssh.send(f"{command}\n")
                ssh.settimeout(5)

                output = ""
                while True:
                    try:
                        part = ssh.recv(max_bytes).decode("utf-8")
                        output += part
                        time.sleep(0.5)
                    except socket.timeout:
                        break
                result[command] = output

            return result


    if __name__ == "__main__":
        devices = ["192.168.100.1", "192.168.100.2", "192.168.100.3"]
        commands = ["sh clock", "sh arp"]
        result = send_show_command("192.168.100.1", "cisco", "cisco", "cisco", commands)
        pprint(result, width=120)



Результат выполнения скрипта:

::

    {'sh arp': 'sh arp\r\n'
               'Protocol  Address          Age (min)  Hardware Addr   Type   Interface\r\n'
               'Internet  192.168.100.1           -   aabb.cc00.6500  ARPA   Ethernet0/0\r\n'
               'Internet  192.168.100.2         124   aabb.cc00.6600  ARPA   Ethernet0/0\r\n'
               'Internet  192.168.100.3         183   aabb.cc00.6700  ARPA   Ethernet0/0\r\n'
               'Internet  192.168.100.100       208   aabb.cc80.c900  ARPA   Ethernet0/0\r\n'
               'Internet  192.168.101.1           -   aabb.cc00.6500  ARPA   Ethernet0/0\r\n'
               'Internet  192.168.102.1           -   aabb.cc00.6500  ARPA   Ethernet0/0\r\n'
               'Internet  192.168.130.1           -   aabb.cc00.6530  ARPA   Ethernet0/3\r\n'
               'Internet  192.168.200.1           -   0203.e800.6510  ARPA   Ethernet0/1\r\n'
               'Internet  192.168.200.100        18   6ee2.6d8c.e75d  ARPA   Ethernet0/1\r\n'
               'R1#',
     'sh clock': 'sh clock\r\n*08:25:22.435 UTC Mon Jul 20 2020\r\nR1#'}


Постраничный вывод команд
~~~~~~~~~~~~~~~~~~~~~~~~~

Пример использования paramiko для работы с постраничным выводом команд
show (файл 3_paramiko_more.py):

.. code:: python

    import paramiko
    import time
    import socket
    from pprint import pprint
    import re


    def send_show_command(
        ip,
        username,
        password,
        enable,
        command,
        max_bytes=60000,
        short_pause=1,
        long_pause=5,
    ):
        cl = paramiko.SSHClient()
        cl.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        cl.connect(
            hostname=ip,
            username=username,
            password=password,
            look_for_keys=False,
            allow_agent=False,
        )
        with cl.invoke_shell() as ssh:
            ssh.send("enable\n")
            ssh.send(enable + "\n")
            time.sleep(short_pause)
            ssh.recv(max_bytes)

            result = {}
            for command in commands:
                ssh.send(f"{command}\n")
                ssh.settimeout(5)

                output = ""
                while True:
                    try:
                        page = ssh.recv(max_bytes).decode("utf-8")
                        output += page
                        time.sleep(0.5)
                    except socket.timeout:
                        break
                    if "More" in page:
                        ssh.send(" ")
                output = re.sub(" +--More--| +\x08+ +\x08+", "\n", output)
                result[command] = output

            return result


    if __name__ == "__main__":
        devices = ["192.168.100.1", "192.168.100.2", "192.168.100.3"]
        commands = ["sh run"]
        result = send_show_command("192.168.100.1", "cisco", "cisco", "cisco", commands)
        pprint(result, width=120)

