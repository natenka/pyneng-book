Модуль pexpect
--------------

Модуль pexpect позволяет автоматизировать интерактивные подключения,
такие как:

* telnet 
* ssh 
* ftp

.. note::

    Pexpect - это реализация expect на Python.

Для начала, модуль pexpect нужно установить:

::

    pip install pexpect


Логика работы pexpect такая: 

* запускается какая-то программа 
* pexpect ожидает определенный вывод (приглашение, запрос пароля и подобное) 
* получив вывод, он отправляет команды/данные 
* последние два действия повторяются столько, сколько нужно

При этом сам pexpect не реализует различные утилиты, а использует уже
готовые.

В pexpect есть два основных инструмента: 

* функция ``run()`` 
* класс ``spawn``

``pexpect.run()``
~~~~~~~~~~~~~~~~~

Функция ``run()`` позволяет вызвать какую-то программу и вернуть
её вывод.

Например:

.. code:: python

    In [1]: import pexpect

    In [2]: output = pexpect.run('ls -ls')

    In [3]: print(output)
    b'total 44\r\n4 -rw-r--r-- 1 vagrant vagrant 3203 Jul 14 07:15 1_pexpect.py\r\n4 -rw-r--r-- 1 vagrant vagrant 3393 Jul 14 07:15 2_telnetlib.py\r\n4 -rw-r--r-- 1 vagrant vagrant 3452 Jul 14 07:15 3_paramiko.py\r\n4 -rw-r--r-- 1 vagrant vagrant 3127 Jul 14 07:15 4_netmiko.py\r\n4 -rw-r--r-- 1 vagrant vagrant  718 Jul 14 07:15 4_netmiko_telnet.py\r\n4 -rw-r--r-- 1 vagrant vagrant  300 Jul  8 15:31 devices.yaml\r\n4 -rw-r--r-- 1 vagrant vagrant  413 Jul 14 07:15 netmiko_function.py\r\n4 -rw-r--r-- 1 vagrant vagrant  876 Jul 14 07:15 netmiko_multiprocessing.py\r\n4 -rw-r--r-- 1 vagrant vagrant 1147 Jul 14 07:15 netmiko_threading_data_list.py\r\n4 -rw-r--r-- 1 vagrant vagrant 1121 Jul 14 07:15 netmiko_threading_data.py\r\n4 -rw-r--r-- 1 vagrant vagrant  671 Jul 14 07:15 netmiko_threading.py\r\n'

    In [4]: print(output.decode('utf-8'))
    total 44
    4 -rw-r--r-- 1 vagrant vagrant 3203 Jul 14 07:15 1_pexpect.py
    4 -rw-r--r-- 1 vagrant vagrant 3393 Jul 14 07:15 2_telnetlib.py
    4 -rw-r--r-- 1 vagrant vagrant 3452 Jul 14 07:15 3_paramiko.py
    4 -rw-r--r-- 1 vagrant vagrant 3127 Jul 14 07:15 4_netmiko.py
    4 -rw-r--r-- 1 vagrant vagrant  718 Jul 14 07:15 4_netmiko_telnet.py
    4 -rw-r--r-- 1 vagrant vagrant  300 Jul  8 15:31 devices.yaml
    4 -rw-r--r-- 1 vagrant vagrant  413 Jul 14 07:15 netmiko_function.py
    4 -rw-r--r-- 1 vagrant vagrant  876 Jul 14 07:15 netmiko_multiprocessing.py
    4 -rw-r--r-- 1 vagrant vagrant 1147 Jul 14 07:15 netmiko_threading_data_list.py
    4 -rw-r--r-- 1 vagrant vagrant 1121 Jul 14 07:15 netmiko_threading_data.py
    4 -rw-r--r-- 1 vagrant vagrant  671 Jul 14 07:15 netmiko_threading.py

``pexpect.spawn``
~~~~~~~~~~~~~~~~~

Класс ``spawn`` поддерживает больше возможностей. Он позволяет
взаимодействовать с вызванной программой, отправляя данные и ожидая
ответ.

Например, таким образом можно инициировать соединение SSH:

.. code:: python

    In [5]: ssh = pexpect.spawn('ssh cisco@192.168.100.1')

После выполнения этой строки, подключение готово. Теперь необходимо
указать какую строку ожидать. В данном случае, надо дождаться запроса о
пароле:

.. code:: python

    In [6]: ssh.expect('[Pp]assword')
    Out[6]: 0

Обратите внимание как описана строка, которую ожидает pexpect:
``[Pp]assword``. Это регулярное выражение, которое описывает строку
password или Password. То есть, методу expect можно передавать
регулярное выражение как аргумент.

Метод expect вернул число 0 в результате работы. Это число указывает,
что совпадение было найдено и что это элемент с индексом ноль. Индекс
тут фигурирует из-за того, что expect можно передавать список строк.
Например, можно передать список с двумя элементами:

.. code:: python

    In [7]: ssh = pexpect.spawn('ssh cisco@192.168.100.1')

    In [8]: ssh.expect(['password', 'Password'])
    Out[8]: 1

Обратите внимание, что теперь возвращается 1. Это значит, что
совпадением было слово Password.

Теперь можно отправлять пароль. Для этого используется команда sendline:

.. code:: python

    In [9]: ssh.sendline('cisco')
    Out[9]: 6

Команда sendline отправляет строку, автоматически добавляет к ней
перевод строки на основе значения os.linesep, а затем возвращает число
указывающее сколько байт было записано.

.. note::

    В pexpect есть несколько вариантов отправки команд, не только
    sendline.

Для того чтобы попасть в режим enable цикл expect-sendline повторяется:

.. code:: python

    In [10]: ssh.expect('[>#]')
    Out[10]: 0

    In [11]: ssh.sendline('enable')
    Out[11]: 7

    In [12]: ssh.expect('[Pp]assword')
    Out[12]: 0

    In [13]: ssh.sendline('cisco')
    Out[13]: 6

    In [14]: ssh.expect('[>#]')
    Out[14]: 0

Теперь можно отправлять команду:

.. code:: python

    In [15]: ssh.sendline('sh ip int br')
    Out[15]: 13

После отправки команды, pexpect надо указать до какого момента считать
вывод. Указываем, что считать надо до #:

.. code:: python

    In [16]: ssh.expect('#')
    Out[16]: 0

Вывод команды находится в атрибуте before:

.. code:: python

    In [17]: ssh.before
    Out[17]: b'sh ip int br\r\nInterface                  IP-Address      OK? Method Status                Protocol\r\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \r\nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \r\nEthernet0/2                19.1.1.1        YES NVRAM  up                    up      \r\nEthernet0/3                192.168.230.1   YES NVRAM  up                    up      \r\nEthernet0/3.100            10.100.0.1      YES NVRAM  up                    up      \r\nEthernet0/3.200            10.200.0.1      YES NVRAM  up                    up      \r\nEthernet0/3.300            10.30.0.1       YES NVRAM  up                    up      \r\nR1'

Так как результат выводится в виде последовательности байтов, надо
конвертировать ее в строку:

.. code:: python

    In [18]: show_output = ssh.before.decode('utf-8')

    In [19]: print(show_output)
    sh ip int br
    Interface                  IP-Address      OK? Method Status                Protocol
    Ethernet0/0                192.168.100.1   YES NVRAM  up                    up
    Ethernet0/1                192.168.200.1   YES NVRAM  up                    up
    Ethernet0/2                19.1.1.1        YES NVRAM  up                    up
    Ethernet0/3                192.168.230.1   YES NVRAM  up                    up
    Ethernet0/3.100            10.100.0.1      YES NVRAM  up                    up
    Ethernet0/3.200            10.200.0.1      YES NVRAM  up                    up
    Ethernet0/3.300            10.30.0.1       YES NVRAM  up                    up
    R1

Завершается сессия вызовом метода close:

.. code:: python

    In [20]: ssh.close()

Специальные символы в shell
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Pexpect не интерпретирует специальные символы shell, такие как ``>``,
``|``, ``*``.

Для того, чтобы, например, команда ``ls -ls | grep SUMMARY`` отработала,
нужно запустить shell таким образом:

.. code:: python

    In [1]: import pexpect

    In [2]: p = pexpect.spawn('/bin/bash -c "ls -ls | grep pexpect"')

    In [3]: p.expect(pexpect.EOF)
    Out[3]: 0

    In [4]: print(p.before)
    b'4 -rw-r--r-- 1 vagrant vagrant 3203 Jul 14 07:15 1_pexpect.py\r\n'

    In [5]: print(p.before.decode('utf-8'))
    4 -rw-r--r-- 1 vagrant vagrant 3203 Jul 14 07:15 1_pexpect.py

pexpect.EOF
~~~~~~~~~~~

В предыдущем примере встретилось использование pexpect.EOF.

.. note::

    EOF (end of file) — конец файла

Это специальное значение, которое позволяет отреагировать на завершение
исполнения команды или сессии, которая была запущена в spawn.

При вызове команды ``ls -ls`` pexpect не получает интерактивный сеанс.
Команда выполняется и всё, на этом завершается её работа.

Поэтому если запустить её и указать в expect приглашение, возникнет
ошибка:

.. code:: python

    In [5]: p = pexpect.spawn('/bin/bash -c "ls -ls | grep SUMMARY"')

    In [6]: p.expect('nattaur')
    ---------------------------------------------------------------------------
    EOF                                       Traceback (most recent call last)
    <ipython-input-9-9c71777698c2> in <module>()
    ----> 1 p.expect('nattaur')
    ...

Если передать в expect EOF, ошибки не будет.

Метод pexpect.expect
~~~~~~~~~~~~~~~~~~~~

В pexpect.expect как шаблон может использоваться: 

* регулярное выражение 
* EOF - этот шаблон позволяет среагировать на исключение EOF
* TIMEOUT - исключение timeout (по умолчанию значение timeout = 30 секунд) 
* compiled re

Еще одна очень полезная возможность pexpect.expect: можно передавать не
одно значение, а список.

Например:

.. code:: python

    In [7]: p = pexpect.spawn('/bin/bash -c "ls -ls | grep netmiko"')

    In [8]: p.expect(['py3_convert', pexpect.TIMEOUT, pexpect.EOF])
    Out[8]: 2

Тут несколько важных моментов: 

* когда pexpect.expect вызывается со списком, можно указывать разные ожидаемые строки 
* кроме строк, можно указывать исключения 
* pexpect.expect возвращает номер элемента списка, который сработал 

  * в данном случае номер 2, так как исключение EOF находится в списке под номером два 

* за счет такого формата можно делать ответвления в программе, 
  в зависимости от того, с каким элементом было совпадение

Пример использования pexpect
----------------------------

Пример использования pexpect для подключения к оборудованию и передачи
команды show (файл 1_pexpect.py):

.. literalinclude:: /pyneng-examples-exercises/examples/19_ssh_telnet/1_pexpect.py
  :language: python
  :linenos:


Комментарии с скрипту: 

* команда, которую нужно выполнить, передается как аргумент 
* затем запрашивается логин, пароль и пароль на режим enable 

  * пароли запрашиваются с помощью модуля getpass 

* ``ip_list`` - это список IP-адресов устройств, к которым будет выполняться подключение
* в цикле выполняется подключение к устройствам из списка 
* в классе spawn выполняется подключение по SSH к текущему адресу, используя 
  указанное имя пользователя 
* после этого начинают чередоваться пары методов: expect и sendline

  * ``expect`` - ожидание подстроки 
  * ``sendline`` - когда строка появилась, отправляется команда 

* так происходит до конца цикла, и только последняя команда отличается: 

  * ``before`` позволяет считать всё, что поймал pexpect до предыдущей 
    подстроки в expect

.. note::

    Обратите внимание на строку ``ssh.expect('[#>]')``. Метод expect
    ожидает не просто строку, а регулярное выражение.

Выполнение скрипта выглядит так:

::

    $ python 1_pexpect.py "sh ip int br"
    Username: nata
    Password:
    Enter enable secret:
    Connection to device 192.168.100.1
    sh ip int br
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
    R1
    Connection to device 192.168.100.2
    sh ip int br
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
    R2
    Connection to device 192.168.100.3
    sh ip int br
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
    R3

Обратите внимание, что, так как в последнем expect указано, что надо
ожидать подстроку ``#``, метод before показал и команду, и имя хоста.

