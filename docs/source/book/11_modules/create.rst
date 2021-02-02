Создание своих модулей
----------------------

Модуль - это файл с расширением .py и кодом Python.

Пример создания своих модулей и импорта функции из одного модуля в другой.

Файл check_ip_function.py:

.. code:: python

    import ipaddress


    def check_ip(ip):
        try:
            ipaddress.ip_address(ip)
            return True
        except ValueError as err:
            return False


    ip1 = '10.1.1.1'
    ip2 = '10.1.1'

    print('Проверка IP...')
    print(ip1, check_ip(ip1))
    print(ip2, check_ip(ip2))

В файле check_ip_function.py создана функция check_ip,
которая проверяет, что аргумент является IP-адресом.
Тут проверка выполняется с помощью модуля ipaddress,
который будет рассматриваться в следующем разделе.

Функция ipaddress.ip_address сама проверяет правильность IP-адреса
и генерирует исключение ValueError, если адрес не прошел проверку.
Функция check_ip возвращает True, если адрес прошел проверку и False - если нет.

Если запустить скрипт check_ip_function.py вывод будет таким:

::

    $ python check_ip_function.py
    Проверка IP...
    10.1.1.1 True
    10.1.1 False


Второй скрипт импортирует функцию check_ip и использует ее для того
чтобы из списка адресов отобрать только те, которые прошли проверку
(файл get_correct_ip.py):

.. code:: python

    from check_ip_function import check_ip


    def return_correct_ip(ip_addresses):
        correct = []
        for ip in ip_addresses:
            if check_ip(ip):
                correct.append(ip)
        return correct

    print('Проверка списка IP-адресов')
    ip_list = ['10.1.1.1', '8.8.8.8', '2.2.2']
    correct = return_correct_ip(ip_list)
    print(correct)


В первой строке выполняется импорт функции check_ip из модуля
check_ip_function.py.


Результат выполнения скрипта:

::

    $ python get_correct_ip.py
    Проверка IP...
    10.1.1.1 True
    10.1.1 False
    Проверка списка IP-адресов
    ['10.1.1.1', '8.8.8.8']

Обратите внимание, что выведена не только информация из скрипта get_correct_ip.py,
но и информация из скрипта check_ip_function.py.
Так происходит из-за того, что любая разновидность import выполняет весь скрипт.
То есть, даже когда импорт выглядит как ``from check_ip_function import check_ip``,
выполняется весь скрипт check_ip_function.py, а не только функция check_ip.
В итоге будут выводиться все сообщения импортируемого скрипта.

Сообщения из импортируемого скрипта не страшны, они мешают и только, хуже когда 
скрипт выполнял что-то типа подключения к оборудованию и при импорте функции из него,
придется ждать пока это подключение выполнится.

В Python есть возможность указать, что некоторые строки не должны выполняться при импорте.
Это рассматривается в следующем подразделе.

Функцию return_correct_ip можно заменить filter или генератором списка,
выше используется более длинный, но скорее всего, пока что более понятный
вариант:

.. code:: python

    In [19]: list(filter(check_ip, ip_list))
    Out[19]: ['10.1.1.1', '8.8.8.8']

    In [20]: [ip for ip in ip_list if check_ip(ip)]
    Out[20]: ['10.1.1.1', '8.8.8.8']

    In [21]: def return_correct_ip(ip_addresses):
        ...:     return [ip for ip in ip_addresses if check_ip(ip)]
        ...:

    In [22]: return_correct_ip(ip_list)
    Out[22]: ['10.1.1.1', '8.8.8.8']

