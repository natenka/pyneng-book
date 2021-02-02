Модуль subprocess
-----------------

Модуль subprocess позволяет создавать новые процессы.
При этом он может подключаться к 
`стандартным потокам ввода/вывода/ошибок <http://xgu.ru/wiki/stdin>`__ 
и получать код возврата.

С помощью subprocess можно, например, выполнять любые команды Linux из
скрипта.
И в зависимости от ситуации получать вывод или только проверять, что
команда выполнилась без ошибок.

.. note::

    В Python 3.5 cинтаксис модуля subprocess изменился.

Функция ``subprocess.run``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Функция ``subprocess.run`` - основной способ работы с модулем
subprocess.

Самый простой вариант использования функции - запуск её таким образом:

.. code:: python

    In [1]: import subprocess

    In [2]: result = subprocess.run('ls')
    ipython_as_mngmt_console.md  README.md         version_control.md
    module_search.md             useful_functions
    naming_conventions           useful_modules

В переменной result теперь содержится специальный объект
CompletedProcess. Из этого объекта можно получить информацию о
выполнении процесса, например, о коде возврата:

.. code:: python

    In [3]: result
    Out[3]: CompletedProcess(args='ls', returncode=0)

    In [4]: result.returncode
    Out[4]: 0

Код 0 означает, что программа выполнилась успешно.

Если необходимо вызвать команду с аргументами, её
нужно передавать таким образом (как список):

.. code:: python

    In [5]: result = subprocess.run(['ls', '-ls'])
    total 28
    4 -rw-r--r-- 1 vagrant vagrant   56 Jun  7 19:35 ipython_as_mngmt_console.md
    4 -rw-r--r-- 1 vagrant vagrant 1638 Jun  7 19:35 module_search.md
    4 drwxr-xr-x 2 vagrant vagrant 4096 Jun  7 19:35 naming_conventions
    4 -rw-r--r-- 1 vagrant vagrant  277 Jun  7 19:35 README.md
    4 drwxr-xr-x 2 vagrant vagrant 4096 Jun 16 05:11 useful_functions
    4 drwxr-xr-x 2 vagrant vagrant 4096 Jun 17 16:28 useful_modules
    4 -rw-r--r-- 1 vagrant vagrant   49 Jun  7 19:35 version_control.md

При попытке выполнить команду с использованием wildcard-выражений,
например, использовать ``*``, возникнет ошибка:

.. code:: python

    In [6]: result = subprocess.run(['ls', '-ls', '*md'])
    ls: cannot access *md: No such file or directory

Чтобы вызывать команды, в которых используются wildcard-выражения, нужно
добавлять аргумент shell и вызывать команду таким образом:

.. code:: python

    In [7]: result = subprocess.run('ls -ls *md', shell=True)
    4 -rw-r--r-- 1 vagrant vagrant   56 Jun  7 19:35 ipython_as_mngmt_console.md
    4 -rw-r--r-- 1 vagrant vagrant 1638 Jun  7 19:35 module_search.md
    4 -rw-r--r-- 1 vagrant vagrant  277 Jun  7 19:35 README.md
    4 -rw-r--r-- 1 vagrant vagrant   49 Jun  7 19:35 version_control.md

Ещё одна особенность функции ``run`` - она ожидает завершения выполнения
команды. Если попробовать, например, запустить команду ping, то этот
аспект будет заметен:

.. code:: python

    In [8]: result = subprocess.run(['ping', '-c', '3', '-n', '8.8.8.8'])
    PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_seq=1 ttl=43 time=55.1 ms
    64 bytes from 8.8.8.8: icmp_seq=2 ttl=43 time=54.7 ms
    64 bytes from 8.8.8.8: icmp_seq=3 ttl=43 time=54.4 ms

    --- 8.8.8.8 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2004ms
    rtt min/avg/max/mdev = 54.498/54.798/55.116/0.252 ms

Получение результата выполнения команды
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

По умолчанию функция run возвращает результат выполнения команды на
стандартный поток вывода.
Если нужно получить результат выполнения команды, надо добавить аргумент
stdout и указать ему значение subprocess.PIPE:

.. code:: python

    In [9]: result = subprocess.run(['ls', '-ls'], stdout=subprocess.PIPE)

Теперь можно получить результат выполнения команды таким образом:

.. code:: python

    In [10]: print(result.stdout)
    b'total 28\n4 -rw-r--r-- 1 vagrant vagrant   56 Jun  7 19:35 ipython_as_mngmt_console.md\n4 -rw-r--r-- 1 vagrant vagrant 1638 Jun  7 19:35 module_search.md\n4 drwxr-xr-x 2 vagrant vagrant 4096 Jun  7 19:35 naming_conventions\n4 -rw-r--r-- 1 vagrant vagrant  277 Jun  7 19:35 README.md\n4 drwxr-xr-x 2 vagrant vagrant 4096 Jun 16 05:11 useful_functions\n4 drwxr-xr-x 2 vagrant vagrant 4096 Jun 17 16:30 useful_modules\n4 -rw-r--r-- 1 vagrant vagrant   49 Jun  7 19:35 version_control.md\n'

Обратите внимание на букву b перед строкой. Она означает, что модуль
вернул вывод в виде байтовой строки.
Для перевода её в unicode есть два варианта:

-  выполнить decode полученной строки
-  указать аргумент encoding

Вариант с decode:

.. code:: python

    In [11]: print(result.stdout.decode('utf-8'))
    total 28
    4 -rw-r--r-- 1 vagrant vagrant   56 Jun  7 19:35 ipython_as_mngmt_console.md
    4 -rw-r--r-- 1 vagrant vagrant 1638 Jun  7 19:35 module_search.md
    4 drwxr-xr-x 2 vagrant vagrant 4096 Jun  7 19:35 naming_conventions
    4 -rw-r--r-- 1 vagrant vagrant  277 Jun  7 19:35 README.md
    4 drwxr-xr-x 2 vagrant vagrant 4096 Jun 16 05:11 useful_functions
    4 drwxr-xr-x 2 vagrant vagrant 4096 Jun 17 16:30 useful_modules
    4 -rw-r--r-- 1 vagrant vagrant   49 Jun  7 19:35 version_control.md

Вариант с encoding:

.. code:: python

    In [12]: result = subprocess.run(['ls', '-ls'], stdout=subprocess.PIPE, encoding='utf-8')

    In [13]: print(result.stdout)
    total 28
    4 -rw-r--r-- 1 vagrant vagrant   56 Jun  7 19:35 ipython_as_mngmt_console.md
    4 -rw-r--r-- 1 vagrant vagrant 1638 Jun  7 19:35 module_search.md
    4 drwxr-xr-x 2 vagrant vagrant 4096 Jun  7 19:35 naming_conventions
    4 -rw-r--r-- 1 vagrant vagrant  277 Jun  7 19:35 README.md
    4 drwxr-xr-x 2 vagrant vagrant 4096 Jun 16 05:11 useful_functions
    4 drwxr-xr-x 2 vagrant vagrant 4096 Jun 17 16:31 useful_modules
    4 -rw-r--r-- 1 vagrant vagrant   49 Jun  7 19:35 version_control.md

Отключение вывода
~~~~~~~~~~~~~~~~~

Иногда достаточно получения кода возврата и нужно отключить вывод
результата выполнения на стандартный поток вывода, и при этом сам
результат не нужен.
Это можно сделать, передав функции run аргумент stdout со значением
subprocess.DEVNULL:

.. code:: python

    In [14]: result = subprocess.run(['ls', '-ls'], stdout=subprocess.DEVNULL)

    In [15]: print(result.stdout)
    None

    In [16]: print(result.returncode)
    0

Работа со стандартным потоком ошибок
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Если команда была выполнена с ошибкой или не отработала корректно, вывод
команды попадет на стандартный поток ошибок.

Получить этот вывод можно так же, как и стандартный поток вывода:

.. code:: python

    In [17]: result = subprocess.run(['ping', '-c', '3', '-n', 'a'], stderr=subprocess.PIPE, encoding='utf-8')

Теперь в result.stdout пустая строка, а в result.stderr находится
стандартный поток вывода:

.. code:: python

    In [18]: print(result.stdout)
    None

    In [19]: print(result.stderr)
    ping: unknown host a


    In [20]: print(result.returncode)
    2

Примеры использования модуля
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Пример использования модуля subprocess (файл subprocess_run_basic.py):

.. code:: python

    import subprocess

    reply = subprocess.run(['ping', '-c', '3', '-n', '8.8.8.8'])

    if reply.returncode == 0:
        print('Alive')
    else:
        print('Unreachable')

Результат выполнения будет таким:

::

    $ python subprocess_run_basic.py
    PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_seq=1 ttl=43 time=54.0 ms
    64 bytes from 8.8.8.8: icmp_seq=2 ttl=43 time=54.4 ms
    64 bytes from 8.8.8.8: icmp_seq=3 ttl=43 time=53.9 ms

    --- 8.8.8.8 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2005ms
    rtt min/avg/max/mdev = 53.962/54.145/54.461/0.293 ms
    Alive

То есть, результат выполнения команды выводится на стандартный поток
вывода.

Функция ping_ip проверяет доступность IP-адреса и возвращает True и
stdout, если адрес доступен, или False и stderr, если адрес недоступен
(файл subprocess_ping_function.py):

.. code:: python

    import subprocess


    def ping_ip(ip_address):
        """
        Ping IP address and return tuple:
        On success:
            * True
            * command output (stdout)
        On failure:
            * False
            * error output (stderr)
        """
        reply = subprocess.run(['ping', '-c', '3', '-n', ip_address],
                               stdout=subprocess.PIPE,
                               stderr=subprocess.PIPE,
                               encoding='utf-8')
        if reply.returncode == 0:
            return True, reply.stdout
        else:
            return False, reply.stderr

    print(ping_ip('8.8.8.8'))
    print(ping_ip('a'))

Результат выполнения будет таким:

::

    $ python subprocess_ping_function.py
    (True, 'PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.\n64 bytes from 8.8.8.8: icmp_seq=1 ttl=43 time=63.8 ms\n64 bytes from 8.8.8.8: icmp_seq=2 ttl=43 time=55.6 ms\n64 bytes from 8.8.8.8: icmp_seq=3 ttl=43 time=55.9 ms\n\n--- 8.8.8.8 ping statistics ---\n3 packets transmitted, 3 received, 0% packet loss, time 2003ms\nrtt min/avg/max/mdev = 55.643/58.492/63.852/3.802 ms\n')
    (False, 'ping: unknown host a\n')

