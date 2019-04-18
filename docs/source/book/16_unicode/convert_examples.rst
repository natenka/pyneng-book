Примеры конвертации между байтами и строками
--------------------------------------------

Рассмотрим несколько примеров работы с байтами и конвертации байт в
строки.

subprocess
~~~~~~~~~~

Модуль subprocess возвращает результат команды в виде байт:

.. code:: python

    In [1]: import subprocess

    In [2]: result = subprocess.run(['ping', '-c', '3', '-n', '8.8.8.8'],
       ...:                         stdout=subprocess.PIPE)
       ...:

    In [3]: result.stdout
    Out[3]: b'PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.\n64 bytes from 8.8.8.8: icmp_seq=1 ttl=43 time=59.4 ms\n64 bytes from 8.8.8.8: icmp_seq=2 ttl=43 time=54.4 ms\n64 bytes from 8.8.8.8: icmp_seq=3 ttl=43 time=55.1 ms\n\n--- 8.8.8.8 ping statistics ---\n3 packets transmitted, 3 received, 0% packet loss, time 2002ms\nrtt min/avg/max/mdev = 54.470/56.346/59.440/2.220 ms\n'

Если дальше необходимо работать с этим выводом, надо сразу
конвертировать его в строку:

.. code:: python

    In [4]: output = result.stdout.decode('utf-8')

    In [5]: print(output)
    PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_seq=1 ttl=43 time=59.4 ms
    64 bytes from 8.8.8.8: icmp_seq=2 ttl=43 time=54.4 ms
    64 bytes from 8.8.8.8: icmp_seq=3 ttl=43 time=55.1 ms

    --- 8.8.8.8 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2002ms
    rtt min/avg/max/mdev = 54.470/56.346/59.440/2.220 ms

Модуль subprocess поддерживает еще один вариант преобразования -
параметр encoding. Если указать его при вызове функции run, результат
будет получен в виде строки:

.. code:: python

    In [6]: result = subprocess.run(['ping', '-c', '3', '-n', '8.8.8.8'],
       ...:                         stdout=subprocess.PIPE, encoding='utf-8')
       ...:

    In [7]: result.stdout
    Out[7]: 'PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.\n64 bytes from 8.8.8.8: icmp_seq=1 ttl=43 time=55.5 ms\n64 bytes from 8.8.8.8: icmp_seq=2 ttl=43 time=54.6 ms\n64 bytes from 8.8.8.8: icmp_seq=3 ttl=43 time=53.3 ms\n\n--- 8.8.8.8 ping statistics ---\n3 packets transmitted, 3 received, 0% packet loss, time 2003ms\nrtt min/avg/max/mdev = 53.368/54.534/55.564/0.941 ms\n'

    In [8]: print(result.stdout)
    PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_seq=1 ttl=43 time=55.5 ms
    64 bytes from 8.8.8.8: icmp_seq=2 ttl=43 time=54.6 ms
    64 bytes from 8.8.8.8: icmp_seq=3 ttl=43 time=53.3 ms

    --- 8.8.8.8 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2003ms
    rtt min/avg/max/mdev = 53.368/54.534/55.564/0.941 ms

telnetlib
~~~~~~~~~

В зависимости от модуля, преобразование между строками и байтами может
выполняться автоматически, а может требоваться явно.

Например, в модуле telnetlib необходимо передавать байты в методах
read\_until и write:

.. code:: python

    import telnetlib
    import time
     
    t = telnetlib.Telnet('192.168.100.1')
     
    t.read_until(b'Username:')
    t.write(b'cisco\n')
     
    t.read_until(b'Password:')
    t.write(b'cisco\n')
    t.write(b'sh ip int br\n')
     
    time.sleep(5)
     
    output = t.read_very_eager().decode('utf-8')
    print(output)

И возвращает метод байты, поэтому в предпоследней строке используется
decode.

pexpect
~~~~~~~

Модуль pexpect как аргумент ожидает строку, а возвращает байты:

.. code:: python

    In [9]: import pexpect

    In [10]: output = pexpect.run('ls -ls')

    In [11]: output
    Out[11]: b'total 8\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug 28 12:16 concurrent_futures\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug  3 07:59 iterator_generator\r\n'

    In [12]: output.decode('utf-8')
    Out[12]: 'total 8\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug 28 12:16 concurrent_futures\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug  3 07:59 iterator_generator\r\n'

И также поддерживает вариант передачи кодировки через параметр encoding:

.. code:: python

    In [13]: output = pexpect.run('ls -ls', encoding='utf-8')

    In [14]: output
    Out[14]: 'total 8\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug 28 12:16 concurrent_futures\r\n4 drwxr-xr-x 2 vagrant vagrant 4096 Aug  3 07:59 iterator_generator\r\n'

Работа с файлами
~~~~~~~~~~~~~~~~

До сих пор при работе с файлами использовалась такая конструкция:

.. code:: python

    with open(filename) as f:
        for line in f:
            print(line)

Но на самом деле, при чтении файла происходит конвертация байт в строки.
И при этом использовалась кодировка по умолчанию:

.. code:: python

    In [1]: import locale

    In [2]: locale.getpreferredencoding()
    Out[2]: 'UTF-8'

Кодировка по умолчанию в файле:

.. code:: python

    In [2]: f = open('r1.txt')

    In [3]: f
    Out[3]: <_io.TextIOWrapper name='r1.txt' mode='r' encoding='UTF-8'>

При работе с файлами лучше явно указывать кодировку, так как в разных ОС
она может отличаться:

.. code:: python

    In [4]: with open('r1.txt', encoding='utf-8') as f:
       ...:     for line in f:
       ...:         print(line, end='')
       ...:
    !
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    !
    no ip domain lookup
    !
    ip ssh version 2
    !

Выводы
~~~~~~

Эти примеры показаны тут для того, чтобы показать, что разные модули
могут по-разному подходить к вопросу конвертации между строками и
байтами. И разные функции и методы этих модулей могут ожидать аргументы
и возвращать значения разных типов. Однако все эти вещи написаны в
документации.
