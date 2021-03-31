Модуль scrapli
--------------

scrapli это модуль, который позволяет подключаться к сетевому оборудованию
используя Telnet, SSH или NETCONF.

Также как и netmiko, scrapli может использовать paramiko или telnetlib
(и другие модули) для самого подключения, но при этом предоставляет одинаковый
интерфейс работы для разных типов подключения и разного оборудования.

Три основные составляющие части scrapli:

* transport - это конкретный способ подключения к оборудованию
* channel - следующий уровень над транспортом, который отвечает за отправку команд,
  получение вывода и другими взаимодействиями с оборудованием
* driver - это интерфейс, который предоставляется пользователю для работы со scrapli.
  Тут есть как специфические драйверы типа ``IOSXEDriver``, который понимает
  как взаимодействовать с оборудованием конкретного типа, так и базовый
  драйвер Driver, который предоставляет минимальный интерфейс для работы через SSH/Telnet.

Доступные варианты транспорта:

* system
* paramiko
* ssh2
* telnet
* asyncssh
* asynctelnet

Основные примеры будут с использованием транспорта system. Так как интерфейс
модуля одинаковый для всех синхронных вариантов транспорта, для использовании
другого вида транспорта, надо только указать его (для транспорта telnet еще
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

Пример подключения с использованием драйвера IOSXEDriver:

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

Метод send_command
~~~~~~~~~~~~~~~~~~

.. code:: python

    In [19]: reply = ssh.send_command("sh clock")

    In [20]: reply
    Out[20]: Response <Success: True>

    In [21]: reply.result
    Out[21]: '*17:31:54.232 UTC Wed Mar 31 2021'

    In [22]: reply.failed
    Out[22]: False

    In [23]: reply.raw_result
    Out[23]: b'\n*17:31:54.232 UTC Wed Mar 31 2021\nR1#'


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



