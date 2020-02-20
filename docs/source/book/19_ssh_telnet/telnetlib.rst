Модуль telnetlib
----------------

Модуль telnetlib входит в стандартную библиотеку Python. Это реализация
клиента telnet.

.. note::

    Подключиться по telnet можно и используя pexpect. Плюс telnetlib в
    том, что этот модуль входит в стандартную библиотеку Python.

Принцип работы telnetlib напоминает pexpect, но есть несколько отличий.
Самое заметное отличие в том, что telnetlib требует передачи байтовой
строки, а не обычной.

Подключение выполняется таким образом:

.. code:: python

    In [1]: telnet = telnetlib.Telnet('192.168.100.1')

С помощью метода read_until указывается до какой строки считать вывод.
При этом, как аргумент надо передавать не обычную строку, а байты:

.. code:: python

    In [2]: telnet.read_until(b'Username')
    Out[2]: b'\r\n\r\nUser Access Verification\r\n\r\nUsername'

Метод read_until возвращает все, что он считал до указанной строки.

Для передачи данных используется метод write. Ему нужно передавать
байтовую строку:

.. code:: python

    In [3]: telnet.write(b'cisco\n')

Читаем вывод до слова Password и передаем пароль:

.. code:: python

    In [4]: telnet.read_until(b'Password')
    Out[4]: b': cisco\r\nPassword'

    In [5]: telnet.write(b'cisco\n')

Теперь можно указать, что надо считать вывод до приглашения, а затем
отправить команду:

.. code:: python

    In [6]: telnet.read_until(b'>')
    Out[6]: b': \r\nR1>'

    In [7]: telnet.write(b'sh ip int br\n')

После отправки команды можно продолжать использовать метод read_until:

.. code:: python

    In [8]: telnet.read_until(b'>')
    Out[8]: b'sh ip int br\r\nInterface                  IP-Address      OK? Method Status                Protocol\r\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \r\nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \r\nEthernet0/2                19.1.1.1        YES NVRAM  up                    up      \r\nEthernet0/3                192.168.230.1   YES NVRAM  up                    up      \r\nEthernet0/3.100            10.100.0.1      YES NVRAM  up                    up      \r\nEthernet0/3.200            10.200.0.1      YES NVRAM  up                    up      \r\nEthernet0/3.300            10.30.0.1       YES NVRAM  up                    up      \r\nR1>'

Или использовать еще один метод для чтения read_very_eager.
При использовании метода read_very_eager, можно отправить несколько
команд, а затем считать весь доступный вывод:

.. code:: python

    In [9]: telnet.write(b'sh arp\n')

    In [10]: telnet.write(b'sh clock\n')

    In [11]: telnet.write(b'sh ip int br\n')

    In [12]: all_result = telnet.read_very_eager().decode('utf-8')

    In [13]: print(all_result)
    sh arp
    Protocol  Address          Age (min)  Hardware Addr   Type   Interface
    Internet  10.30.0.1               -   aabb.cc00.6530  ARPA   Ethernet0/3.300
    Internet  10.100.0.1              -   aabb.cc00.6530  ARPA   Ethernet0/3.100
    Internet  10.200.0.1              -   aabb.cc00.6530  ARPA   Ethernet0/3.200
    Internet  19.1.1.1                -   aabb.cc00.6520  ARPA   Ethernet0/2
    Internet  192.168.100.1           -   aabb.cc00.6500  ARPA   Ethernet0/0
    Internet  192.168.100.2         124   aabb.cc00.6600  ARPA   Ethernet0/0
    Internet  192.168.100.3         143   aabb.cc00.6700  ARPA   Ethernet0/0
    Internet  192.168.100.100       160   aabb.cc80.c900  ARPA   Ethernet0/0
    Internet  192.168.200.1           -   0203.e800.6510  ARPA   Ethernet0/1
    Internet  192.168.200.100        13   0800.27ac.16db  ARPA   Ethernet0/1
    Internet  192.168.230.1           -   aabb.cc00.6530  ARPA   Ethernet0/3
    R1>sh clock
    *19:18:57.980 UTC Fri Nov 3 2017
    R1>sh ip int br
    Interface                  IP-Address      OK? Method Status                Protocol
    Ethernet0/0                192.168.100.1   YES NVRAM  up                    up
    Ethernet0/1                192.168.200.1   YES NVRAM  up                    up
    Ethernet0/2                19.1.1.1        YES NVRAM  up                    up
    Ethernet0/3                192.168.230.1   YES NVRAM  up                    up
    Ethernet0/3.100            10.100.0.1      YES NVRAM  up                    up
    Ethernet0/3.200            10.200.0.1      YES NVRAM  up                    up
    Ethernet0/3.300            10.30.0.1       YES NVRAM  up                    up
    R1>

.. warning::

    Перед методом read_very_eager всегда надо ставить time.sleep(n).

С read_until будет немного другой подход. Можно выполнить те же три
команды, но затем получать вывод по одной за счет чтения до строки с
приглашением:

.. code:: python

    In [14]: telnet.write(b'sh arp\n')

    In [15]: telnet.write(b'sh clock\n')

    In [16]: telnet.write(b'sh ip int br\n')

    In [17]: telnet.read_until(b'>')
    Out[17]: b'sh arp\r\nProtocol  Address          Age (min)  Hardware Addr   Type   Interface\r\nInternet  10.30.0.1               -   aabb.cc00.6530  ARPA   Ethernet0/3.300\r\nInternet  10.100.0.1              -   aabb.cc00.6530  ARPA   Ethernet0/3.100\r\nInternet  10.200.0.1              -   aabb.cc00.6530  ARPA   Ethernet0/3.200\r\nInternet  19.1.1.1                -   aabb.cc00.6520  ARPA   Ethernet0/2\r\nInternet  192.168.100.1           -   aabb.cc00.6500  ARPA   Ethernet0/0\r\nInternet  192.168.100.2         126   aabb.cc00.6600  ARPA   Ethernet0/0\r\nInternet  192.168.100.3         145   aabb.cc00.6700  ARPA   Ethernet0/0\r\nInternet  192.168.100.100       162   aabb.cc80.c900  ARPA   Ethernet0/0\r\nInternet  192.168.200.1           -   0203.e800.6510  ARPA   Ethernet0/1\r\nInternet  192.168.200.100        15   0800.27ac.16db  ARPA   Ethernet0/1\r\nInternet  192.168.230.1           -   aabb.cc00.6530  ARPA   Ethernet0/3\r\nR1>'

    In [18]: telnet.read_until(b'>')
    Out[18]: b'sh clock\r\n*19:20:39.388 UTC Fri Nov 3 2017\r\nR1>'

    In [19]: telnet.read_until(b'>')
    Out[19]: b'sh ip int br\r\nInterface                  IP-Address      OK? Method Status                Protocol\r\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \r\nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \r\nEthernet0/2                19.1.1.1        YES NVRAM  up                    up      \r\nEthernet0/3                192.168.230.1   YES NVRAM  up                    up      \r\nEthernet0/3.100            10.100.0.1      YES NVRAM  up                    up      \r\nEthernet0/3.200            10.200.0.1      YES NVRAM  up                    up      \r\nEthernet0/3.300            10.30.0.1       YES NVRAM  up                    up      \r\nR1>'

Важное отличие между read_until и read_very_eager заключается в том,
как они реагируют на отсутствие вывода.

Метод read_until ждет определенную строку. По умолчанию, если ее нет,
метод "зависнет". Опциональный параметр timeout позволяет указать
сколько ждать нужную строку:

.. code:: python

    In [20]: telnet.read_until(b'>', timeout=5)
    Out[20]: b''

Если за указанное время строка не появилась, возвращается пустая строка.

Метод read_very_eager просто вернет пустую строку, если вывода нет:

.. code:: python

    In [21]: telnet.read_very_eager()
    Out[21]: b''

Метод expect позволяет указывать список с регулярными выражениями. Он
работает похоже на pexpect, но в модуле telnetlib всегда надо передавать
список регулярных выражений.

При этом, можно передавать байтовые строки или компилированные
регулярные выражения:

.. code:: python

    In [22]: telnet.write(b'sh clock\n')

    In [23]: telnet.expect([b'[>#]'])
    Out[23]:
    (0,
     <_sre.SRE_Match object; span=(46, 47), match=b'>'>,
     b'sh clock\r\n*19:35:10.984 UTC Fri Nov 3 2017\r\nR1>')

Метод expect возвращает кортеж их трех элементов: 

* индекс выражения, которое совпало 
* объект Match 
* байтовая строка, которая содержит все 
  считанное до регулярного выражения и включая его

Соответственно, при необходимости, с этими элементами можно дальше
работать:

.. code:: python

    In [24]: telnet.write(b'sh clock\n')

    In [25]: regex_idx, match, output = telnet.expect([b'[>#]'])

    In [26]: regex_idx
    Out[26]: 0

    In [27]: match.group()
    Out[27]: b'>'

    In [28]: match
    Out[28]: <_sre.SRE_Match object; span=(46, 47), match=b'>'>

    In [29]: match.group()
    Out[29]: b'>'

    In [30]: output
    Out[30]: b'sh clock\r\n*19:37:21.577 UTC Fri Nov 3 2017\r\nR1>'

    In [31]: output.decode('utf-8')
    Out[31]: 'sh clock\r\n*19:37:21.577 UTC Fri Nov 3 2017\r\nR1>'

Закрывается соединение методом close:

.. code:: python

    In [32]: telnet.close()

Пример использования telnetlib
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Принцип работы telnetlib напоминает pexpect, поэтому пример ниже должен
быть понятен.

Файл 2_telnetlib.py:

.. literalinclude:: /pyneng-examples-exercises/examples/19_ssh_telnet/2_telnetlib.py
  :language: python
  :linenos:

telnetlib очень похож на pexpect: 

  * ``with telnetlib.Telnet(ip) as t`` - класс Telnet представляет соединение к серверу. 
  * в данном случае ему передается только IP-адрес, но можно передать и порт, 
    к которому нужно подключаться 
  * ``read_until`` - похож на метод ``expect`` в модуле pexpect. 
    Указывает, до какой строки следует считывать вывод 
  * ``write`` - передать строку 
  * ``read_very_eager`` - считать всё, что получается

.. note::

    Использование объекта Telnet как менеджера контекса добавлено в
    версии 3.6

Выполнение скрипта:

::

    $ python 2_telnetlib.py "sh ip int br"
    Username: cisco
    Password:
    Enter enable secret:
    Connection to device 192.168.100.1

    R1#terminal length 0
    R1#sh ip int br
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
    R1#
    Connection to device 192.168.100.2

    R2#terminal length 0
    R2#sh ip int br
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
    R2#
    Connection to device 192.168.100.3

    R3#terminal length 0
    R3#sh ip int br
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
    R3#

