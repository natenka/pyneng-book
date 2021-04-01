Модуль scrapli
--------------

`scrapli <https://github.com/carlmontanari/scrapli>`__ это модуль, который
позволяет подключаться к сетевому оборудованию используя Telnet, SSH или NETCONF.

Также как и netmiko, scrapli может использовать paramiko или telnetlib
(и другие модули) для самого подключения, но при этом предоставляет одинаковый
интерфейс работы для разных типов подключения и разного оборудования.

Установка scrapli:

::

    pip install scrapli


.. note::

    Рассматривается scrapli версии 2021.1.30.

Три основные составляющие части scrapli:

* transport - это конкретный способ подключения к оборудованию
* channel - следующий уровень над транспортом, который отвечает за отправку команд,
  получение вывода и другими взаимодействиями с оборудованием
* driver - это интерфейс, который предоставляется пользователю для работы со scrapli.
  Тут есть как специфические драйверы типа ``IOSXEDriver``, который понимает
  как взаимодействовать с оборудованием конкретного типа, так и базовый
  драйвер Driver, который предоставляет минимальный интерфейс для работы через SSH/Telnet.

Доступные варианты транспорта:

* system - используется встроенный SSH клиент, подразумевается использование клиента на Linux/MacOS
* paramiko - модуль paramiko
* ssh2 - используется модуль ssh2-python (обертка вокруг C библиотеки libssh2)
* telnet - будет использоваться telnetlib
* asyncssh - модуль asyncssh
* asynctelnet - telnet клиент написанный с использованием asyncio

Основные примеры будут с использованием транспорта system. Так как интерфейс
модуля одинаковый для всех синхронных вариантов транспорта, для использовании
другого вида транспорта, надо только указать его (для транспорта telnet
также обязательно указать порт).

.. note::

    Асинхронные варианты транспорта (asyncssh, asynctelnet) рассматриваются в книге
    `Advanced Python для сетевых инженеров <https://advpyneng.readthedocs.io/ru/latest/book/17_async_libraries/scrapli.html>`__


Поддерживаемые платформы:

* Cisco IOS-XE
* Cisco NX-OS
* Juniper JunOS
* Cisco IOS-XR
* Arista EOS

.. note::

    Кроме этих платформ, есть также платформы
    `scrapli community <https://github.com/scrapli/scrapli_community>`__.

В scrapli глобально есть два варианта подключения: используя общий класс Scrapli,
который выбирает нужный driver по параметру platform или конкретный driver,
например, IOSXEDriver. При этом параметры передаются те же самые и конкретному
драйверу и Scrapli.


Параметры подключения
~~~~~~~~~~~~~~~~~~~~~

Основные параметры подключения:

* host - IP-адрес или имя хоста
* auth_username - имя пользователя
* auth_password - пароль
* auth_secondary - пароль на enable
* auth_strict_key - включить/отключить аутентификацию по ключам (по умолчанию включена)
* platform - нужно указывать при использовании Scrapli
* transport - какой транспорт использовать
* transport_options - опции для конкретного транспорта

Процесс подключения немного отличается в зависимости от того используется
менеджер контекста или нет. При подключении без менеджера контекста, сначала надо
передать параметры драйверу или Scrapli, а затем вызвать метод open:

.. code:: python

    from scrapli import Scrapli

    r1 = {
       "host": "192.168.100.1",
       "auth_username": "cisco",
       "auth_password": "cisco",
       "auth_secondary": "cisco",
       "auth_strict_key": False,
       "platform": "cisco_iosxe"
    }

    In [2]: ssh = Scrapli(**r1)

    In [3]: ssh.open()

После этого можно отправлять команды:

.. code:: python

    In [4]: ssh.get_prompt()
    Out[4]: 'R1#'

    In [5]: ssh.close()


При использовании менеджера контекста, open вызывать не надо:

.. code:: python

    In [8]: with Scrapli(**r1_driver) as ssh:
       ...:     print(ssh.get_prompt())
       ...:
    R1#

Использование драйвера
~~~~~~~~~~~~~~~~~~~~~~

Доступные драйверы

+--------------+--------------+-------------------+
| Оборудование | Драйвер      | Параметр platform |
+==============+==============+===================+
| Cisco IOS-XE | IOSXEDriver  | cisco_iosxe       |
+--------------+--------------+-------------------+
| Cisco NX-OS  | NXOSDriver   | cisco_nxos        |
+--------------+--------------+-------------------+
| Cisco IOS-XR | IOSXRDriver  | cisco_iosxr       |
+--------------+--------------+-------------------+
| Arista EOS   | EOSDriver    | arista_eos        |
+--------------+--------------+-------------------+
| Juniper JunOS| JunosDriver  | juniper_junos     |
+--------------+--------------+-------------------+

Пример подключения с использованием драйвера IOSXEDriver (технически
подключение выполняется к Cisco IOS):

.. code:: python

    In [11]: from scrapli.driver.core import IOSXEDriver

    In [12]: r1_driver = {
        ...:    "host": "192.168.100.1",
        ...:    "auth_username": "cisco",
        ...:    "auth_password": "cisco",
        ...:    "auth_secondary": "cisco",
        ...:    "auth_strict_key": False,
        ...: }

    In [13]: with IOSXEDriver(**r1_driver) as ssh:
        ...:     print(ssh.get_prompt())
        ...:
    R1#

Отправка команд
~~~~~~~~~~~~~~~

В scrapli есть несколько методов для отправки команд:

* ``send_command`` - отправить одну show команду 
* ``send_commands`` - отправить список show команд
* ``send_commands_from_file`` - отправить show команды из файла
* ``send_config`` - отправить одну команду в конфигурационном режиме 
* ``send_configs`` - отправить список команд в конфигурационном режиме 
* ``send_configs_from_file`` - отправить команды из файла в конфигурационном режиме
* ``send_interactive``

Все эти методы возвращают объект Response, а не вывод команды в виде строки.

Объект Response
~~~~~~~~~~~~~~~

Метод send_command и другие методы для отправки команд на оборудование
возвращают объект Response (не вывод команды).
Response позволяет получить не только вывод команды, но и такие вещи как
время работы команды, выполнилась команда с ошибками или без, структурированный
вывод с помощью textfsm и так далее.

.. code:: python

    In [15]: reply = ssh.send_command("sh clock")

    In [16]: reply
    Out[16]: Response <Success: True>

Получить вывод команды можно обратившись к атрибуту result:

.. code:: python

    In [17]: reply.result
    Out[17]: '*17:31:54.232 UTC Wed Mar 31 2021'

Атрибут raw_result содержит байтовую строку с полным выводом:

.. code:: python

    In [18]: reply.raw_result
    Out[18]: b'\n*17:31:54.232 UTC Wed Mar 31 2021\nR1#'

Для команд, которые выполняются дольше обычных show, может быть необходимо
знать время выполнения команды:

.. code:: python

    In [18]: r = ssh.send_command("ping 10.1.1.1")

    In [19]: r.result
    Out[19]: 'Type escape sequence to abort.\nSending 5, 100-byte ICMP Echos to 10.1.1.1, timeout is 2 seconds:\n.....\nSuccess rate is 0 percent (0/5)'

    In [20]: r.elapsed_time
    Out[20]: 10.047594

    In [21]: r.start_time
    Out[21]: datetime.datetime(2021, 4, 1, 7, 10, 56, 63697)

    In [22]: r.finish_time
    Out[22]: datetime.datetime(2021, 4, 1, 7, 11, 6, 111291)

Атрибут channel_input возвращает команду, которая была отправлена на оборудование:

.. code:: python

    In [23]: r.channel_input
    Out[23]: 'ping 10.1.1.1'


Метод send_command
~~~~~~~~~~~~~~~~~~

Метод ``send_command`` позволяет отправить одну команду на устройство.

.. code:: python

    In [14]: reply = ssh.send_command("sh clock")

Параметры метода (все эти параметры надо передавать как ключевые):

* ``strip_prompt`` - удалить приглашение из вывода. По умолчанию удаляется 
* ``failed_when_contains`` - если вывод содержит указанную строку или одну из
  строк в списке, будет считаться, что команда выполнилась с ошибкой
* ``timeout_ops`` - максимальное время на выполнение команды, по умолчанию
  равно 30 секунд для IOSXEDriver

Пример вызова метода ``send_command``:

.. code:: python

    In [15]: reply = ssh.send_command("sh clock")

    In [16]: reply
    Out[16]: Response <Success: True>

Параметр timeout_ops указывает сколько ждать выполнения команды:

.. code:: python

    In [19]: ssh.send_command("ping 8.8.8.8", timeout_ops=20)
    Out[19]: Response <Success: True>

Если команда не выполнилась за указанное время, сгенерируется исключение
ScrapliTimeout (вывод сокращен):

.. code:: python

    In [20]: ssh.send_command("ping 8.8.8.8", timeout_ops=2)
    ---------------------------------------------------------------------------
    ScrapliTimeout                            Traceback (most recent call last)
    <ipython-input-20-e062fb19f0e6> in <module>
    ----> 1 ssh.send_command("ping 8.8.8.8", timeout_ops=2)

Метод send_config
~~~~~~~~~~~~~~~~~

.. code:: python

    In [13]: reply = conn.send_config("logging 10.1.1.200")


Метод send_commands
~~~~~~~~~~~~~~~~~~~

* strip_prompt: bool = True,
* failed_when_contains: Union[str, List[str], NoneType] = None,
* stop_on_failed: bool = False,
* eager: bool = False,



Обнаружение ошибок
~~~~~~~~~~~~~~~~~~

.. code:: python

    In [13]: reply = conn.send_command("sh clck")

    In [14]: reply.result
    Out[14]: "        ^\n% Invalid input detected at '^' marker."

    In [15]: reply
    Out[15]: Response <Success: False>

    In [16]: reply.failed
    Out[16]: True

    In [17]: reply.failed_when_contains
    Out[17]:
    ['% Ambiguous command',
     '% Incomplete command',
     '% Invalid input detected',
     '% Unknown command']

    In [19]: reply.raw_result
    Out[19]: b"\n        ^\n% Invalid input detected at '^' marker.\n\nR1#"

Подключение telnet
~~~~~~~~~~~~~~~~~~

.. code:: python

    from scrapli.driver.core import IOSXEDriver

    r1 = {
        "host": "192.168.100.1",
        "auth_username": "cisco",
        "auth_password": "cisco",
        "auth_secondary": "cisco",
        "auth_strict_key": False,
        "transport": "telnet",
        "port": 23, # обязательно указывать при подключении telnet
    }


    def send_show(device, show_command):
        with IOSXEDriver(**r1) as ssh:
            reply = ssh.send_command(show_command)
            return reply.result


    if __name__ == "__main__":
        output = send_show(r1, "sh ip int br")
        print(output)

