Примеры работы с файлами
-------------------

В этом подразделе рассматривается работа с файлами и объединяются
темы: файлы, циклы и условия.

При обработке вывода команд или конфигурации часто надо будет записать
итоговые данные в словарь.
Не всегда очевидно, как обрабатывать вывод команд и каким образом в целом
подходить к разбору вывода на части. В этом подразделе рассматриваются
несколько примеров, с возрастающим уровнем сложности.

Разбор вывода столбцами
~~~~~~~~~~~~~~~~~~~~~~~

В этом примере будет разбираться вывод команды sh ip int br. Из вывода
команды нам надо получить соответствия имя интерфейса - IP-адрес. То
есть имя интерфейса - это ключ словаря, а IP-адрес - значение. При этом,
соответствие надо делать только для тех интерфейсов, у которых назначен
IP-адрес.

Пример вывода команды sh ip int br (файл sh_ip_int_br.txt):

::

    R1#show ip interface brief
    Interface                  IP-Address      OK? Method Status                Protocol
    FastEthernet0/0            15.0.15.1       YES manual up                    up
    FastEthernet0/1            10.0.12.1       YES manual up                    up
    FastEthernet0/2            10.0.13.1       YES manual up                    up
    FastEthernet0/3            unassigned      YES unset  up                    down
    Loopback0                  10.1.1.1        YES manual up                    up
    Loopback100                100.0.0.1       YES manual up                    up

Файл working_with_dict_example_1.py:

.. code:: python

    result = {}

    with open('sh_ip_int_br.txt') as f:
        for line in f:
            line = line.split()
            if line and line[1][0].isdigit():
                interface, address, *other = line
                result[interface] = address

    print(result)

Команда sh ip int br отображает вывод столбцами. Значит нужные поля
находятся в одной строке. Скрипт обрабатывает вывод построчно и каждую
строку разбивает с помощью метода split.

Полученный в итоге список содержит столбцы вывода. Так как из всего
вывода нужны только интерфейсы на которых настроен IP-адрес, выполняется
проверка первого символа второго столбца: если первый символ число,
значит на интерфейсе назначен адрес и эту строку надо обрабатывать.

В строке ``interface, address, *other = line`` выполняется распаковка
переменных. В переменную interface попадет имя интерфейса, в address
попадет IP-адрес, а в other все остальные поля.

Так как для каждой строки есть пара ключ и значение, они присваиваются в
словарь: ``result[interface] = address``.

Результатом выполнения скрипта будет такой словарь (тут он разбит на
пары ключ-значение для удобства, в реальном выводе скрипта словарь будет
отображаться в одну строку):

.. code:: python

    {'FastEthernet0/0': '15.0.15.1',
     'FastEthernet0/1': '10.0.12.1',
     'FastEthernet0/2': '10.0.13.1',
     'Loopback0': '10.1.1.1',
     'Loopback100': '100.0.0.1'}

Получение ключа и значения из разных строк вывода
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Очень часто вывод команд выглядит таким образом, что ключ и значение
находятся в разных строках. И надо придумать каким образом обрабатывать
вывод, чтобы получить нужное соответствие.

Например, из вывода команды ``sh ip interface`` надо получить соответствие
имя интерфейса - MTU (файл sh_ip_interface.txt):

::

    Ethernet0/0 is up, line protocol is up
      Internet address is 192.168.100.1/24
      Broadcast address is 255.255.255.255
      Address determined by non-volatile memory
      MTU is 1500 bytes
      Helper address is not set
      ...
    Ethernet0/1 is up, line protocol is up
      Internet address is 192.168.200.1/24
      Broadcast address is 255.255.255.255
      Address determined by non-volatile memory
      MTU is 1500 bytes
      Helper address is not set
      ...
    Ethernet0/2 is up, line protocol is up
      Internet address is 19.1.1.1/24
      Broadcast address is 255.255.255.255
      Address determined by non-volatile memory
      MTU is 1500 bytes
      Helper address is not set
      ...

Имя интерфейса находится в строке вида
``Ethernet0/0 is up, line protocol is up``, а MTU в строке вида
``MTU is 1500 bytes``.

Например, попробуем запоминать каждый раз интерфейс и выводить его
значение, когда встречается MTU, вместе со значением MTU:

.. code:: python

    In [2]: with open('sh_ip_interface.txt') as f:
       ...:     for line in f:
       ...:         if 'line protocol' in line:
       ...:             interface = line.split()[0]
       ...:         elif 'MTU is' in line:
       ...:             mtu = line.split()[-2]
       ...:             print('{:15}{}'.format(interface, mtu))
       ...:
    Ethernet0/0    1500
    Ethernet0/1    1500
    Ethernet0/2    1500
    Ethernet0/3    1500
    Loopback0      1514

Вывод организован таким образом, что всегда сначала идет строка с
интерфейсом, а затем через несколько строк - строка с MTU. Если
запоминать имя интерфейса каждый раз, когда оно встречается, то на
момент когда встретится строка с MTU, последний запомненный интерфейс -
это тот к которому относится MTU.

Теперь, если необходимо создать словарь с соответствием интерфейс - MTU,
достаточно записать значения на момент, когда был найден MTU.

Файл working_with_dict_example_2.py:

.. code:: python

    result = {}

    with open('sh_ip_interface.txt') as f:
        for line in f:
            if 'line protocol' in line:
                interface = line.split()[0]
            elif 'MTU is' in line:
                mtu = line.split()[-2]
                result[interface] = mtu

    print(result)

Результатом выполнения скрипта будет такой словарь (тут он разбит на
пары ключ-значение для удобства, в реальном выводе скрипта словарь будет
отображаться в одну строку):

.. code:: python

    {'Ethernet0/0': '1500',
     'Ethernet0/1': '1500',
     'Ethernet0/2': '1500',
     'Ethernet0/3': '1500',
     'Loopback0': '1514'}

Этот прием будет достаточно часто полезен, так как вывод команд, в
целом, организован очень похожим образом.

Вложенный словарь
~~~~~~~~~~~~~~~~~

Если из вывода команды надо получить несколько параметров, очень удобно
использовать словарь с вложенным словарем.

Например, из вывода ```sh ip interface``` надо получить два параметра:
IP-адрес и MTU. Для начала, вывод информации:

::

    Ethernet0/0 is up, line protocol is up
      Internet address is 192.168.100.1/24
      Broadcast address is 255.255.255.255
      Address determined by non-volatile memory
      MTU is 1500 bytes
      Helper address is not set
      ...
    Ethernet0/1 is up, line protocol is up
      Internet address is 192.168.200.1/24
      Broadcast address is 255.255.255.255
      Address determined by non-volatile memory
      MTU is 1500 bytes
      Helper address is not set
      ...
    Ethernet0/2 is up, line protocol is up
      Internet address is 19.1.1.1/24
      Broadcast address is 255.255.255.255
      Address determined by non-volatile memory
      MTU is 1500 bytes
      Helper address is not set
      ...

На первом этапе каждое значение запоминается в переменную, а затем, выводятся все три значения.
Значения выводятся, когда встретилась строка с MTU, потому что она идет последней:

.. code:: python

    In [2]: with open('sh_ip_interface.txt') as f:
       ...:     for line in f:
       ...:         if 'line protocol' in line:
       ...:             interface = line.split()[0]
       ...:         elif 'Internet address' in line:
       ...:             ip_address = line.split()[-1]
       ...:         elif 'MTU' in line:
       ...:             mtu = line.split()[-2]
       ...:             print('{:15}{:17}{}'.format(interface, ip_address, mtu))
       ...:
    Ethernet0/0    192.168.100.1/24 1500
    Ethernet0/1    192.168.200.1/24 1500
    Ethernet0/2    19.1.1.1/24      1500
    Ethernet0/3    192.168.230.1/24 1500
    Loopback0      4.4.4.4/32       1514

Тут используется такой же прием, как в предыдущем примере, но
добавляется еще одна вложенность словаря:

.. code:: python

    result = {}

    with open('sh_ip_interface.txt') as f:
        for line in f:
            if 'line protocol' in line:
                interface = line.split()[0]
                result[interface] = {}
            elif 'Internet address' in line:
                ip_address = line.split()[-1]
                result[interface]['ip'] = ip_address
            elif 'MTU' in line:
                mtu = line.split()[-2]
                result[interface]['mtu'] = mtu

    print(result)

Каждый раз, когда встречается интерфейс, в словаре ```result``` создается ключ
с именем интерфейса, которому соответствует пустой словарь. Эта
заготовка нужна для того, чтобы на момент когда встретится IP-адрес или
MTU можно было записать параметр во вложенный словарь соответствующего
интерфейса.

Результатом выполнения скрипта будет такой словарь (тут он разбит на
пары ключ-значение для удобства, в реальном выводе скрипта словарь будет
отображаться в одну строку):

.. code:: python

    {'Ethernet0/0': {'ip': '192.168.100.1/24', 'mtu': '1500'},
     'Ethernet0/1': {'ip': '192.168.200.1/24', 'mtu': '1500'},
     'Ethernet0/2': {'ip': '19.1.1.1/24', 'mtu': '1500'},
     'Ethernet0/3': {'ip': '192.168.230.1/24', 'mtu': '1500'},
     'Loopback0': {'ip': '4.4.4.4/32', 'mtu': '1514'}}

Вывод с пустыми значениями
~~~~~~~~~~~~~~~~~~~~~~~~~~

Иногда, в выводе будут попадаться секции с пустыми значениями. Например,
в случае с выводом ```sh ip interface```, могут попадаться интерфейсы, которые
выглядят так:

::

    Ethernet0/1 is up, line protocol is up
      Internet protocol processing disabled
    Ethernet0/2 is administratively down, line protocol is down
      Internet protocol processing disabled
    Ethernet0/3 is administratively down, line protocol is down
      Internet protocol processing disabled

Соответственно тут нет MTU или IP-адреса.

И если выполнить предыдущий скрипт для файла с такими интерфейсами,
результат будет таким (вывод для файла sh_ip_interface2.txt):

.. code:: python

    {'Ethernet0/0': {'ip': '192.168.100.2/24', 'mtu': '1500'},
     'Ethernet0/1': {},
     'Ethernet0/2': {},
     'Ethernet0/3': {},
     'Loopback0': {'ip': '2.2.2.2/32', 'mtu': '1514'}}

Если необходимо добавлять интерфейсы в словарь только, когда на
интерфейсе назначен IP-адрес, надо перенести создание ключа с именем
интерфейса на момент, когда встречается строка с IP-адресом (файл
working_with_dict_example_4.py):

.. code:: python

    result = {}

    with open('sh_ip_interface2.txt') as f:
        for line in f:
            if 'line protocol' in line:
                interface = line.split()[0]
            elif 'Internet address' in line:
                ip_address = line.split()[-1]
                result[interface] = {}
                result[interface]['ip'] = ip_address
            elif 'MTU' in line:
                mtu = line.split()[-2]
                result[interface]['mtu'] = mtu

    print(result)

В этом случае результатом будет такой словарь:

.. code:: python

    {'Ethernet0/0': {'ip': '192.168.100.2/24', 'mtu': '1500'},
     'Loopback0': {'ip': '2.2.2.2/32', 'mtu': '1514'}}

