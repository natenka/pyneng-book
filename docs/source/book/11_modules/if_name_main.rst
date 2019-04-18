``if __name__ == "__main__"``
-----------------------------

Достаточно часто скрипт может выполняться и самостоятельно, и может быть
импортирован как модуль другим скриптом.

Например, скрипт filter\_functions.py содержит такой код:

.. code:: python

    from pprint import pprint


    def filter_file_lines(filename, substring):
        result = []
        with open(filename) as f:
            for line in f:
                if substring in line:
                    result.append(line)
        return result


    pprint(filter_file_lines('config_r1.txt', 'ip address'))

В скрипте содержится одна функция, которая отбирает из файла только те
строки, в которых содержится указанная подстрока.

Результат выполнения скрипта:

::

    $ python filter_functions.py
    [' ip address 10.1.1.1 255.255.255.255\n',
     ' ip address 10.0.13.1 255.255.255.0\n',
     ' no ip address\n',
     ' ip address 10.0.19.1 255.255.255.0\n',
     ' no ip address\n',
     ' no ip address\n']

Скрипт get\_data.py импортирует функцию filter\_file\_lines из скрипта
filter\_functions.py и использует её для получения строк в которых
содержится слово interface:

.. code:: python

    from filter_functions import filter_file_lines
    from pprint import pprint

    pprint(filter_file_lines('config_r1.txt', 'interface'))

Выполнение скрипта get\_data.py выглядит таким образом:

::

    $ python get_data.py
    [' ip address 10.1.1.1 255.255.255.255\n',
     ' ip address 10.0.13.1 255.255.255.0\n',
     ' no ip address\n',
     ' ip address 10.0.19.1 255.255.255.0\n',
     ' no ip address\n',
     ' no ip address\n']
    ['interface Loopback0\n',
     'interface Tunnel0\n',
     'interface Ethernet0/0\n',
     'interface Ethernet0/1\n',
     'interface Ethernet0/2\n',
     'interface Ethernet0/3\n',
     'interface Ethernet0/3.100\n',
     'interface Ethernet1/0\n',
     ' event neighbor-discovery interface regexp .*Ethernet.* cdp add\n',
     ' action 3.0 cli command "interface $_nd_local_intf_name"\n']

Полученный вывод содержит не только список со строками, в которых
содержится слово interface, но и вывод из скрипта filter\_functions.py.

Так происходит из-за того, что при импорте модуля, Python выполняет его.

    Python выполняет весь модуль, независимо от того как именно
    импортируется модуль: ``import module``,
    ``from module import function`` или ``from module import *``.

В Python есть специальный прием, который позволяет указать, что какой-то
код должен выполняться, только когда файл запускается напрямую.

Файл filter\_functions.py:

.. code:: python

    from pprint import pprint


    def filter_file_lines(filename, substring):
        result = []
        with open(filename) as f:
            for line in f:
                if substring in line:
                    result.append(line)
        return result


    if __name__ == "__main__":
        pprint(filter_file_lines('config_r1.txt', 'ip address'))

Обратите внимание на запись:

.. code:: python

    if __name__ == '__main__':
        pprint(filter_file_lines('config_r1.txt', 'ip address'))

Переменная ``__name__`` - это специальная переменная, которая будет
равна ``"__main__"``, если файл запускается как основная программа, и
выставляется равной имени модуля, если модуль импортируется.

Таким образом, условие ``if __name__ == '__main__'`` проверяет, был ли
файл запущен напрямую.

Теперь, при выполнении скрипта get\_data.py, вывод такой:

::

    $ python get_data.py
    ['interface Loopback0\n',
     'interface Tunnel0\n',
     'interface Ethernet0/0\n',
     'interface Ethernet0/1\n',
     'interface Ethernet0/2\n',
     'interface Ethernet0/3\n',
     'interface Ethernet0/3.100\n',
     'interface Ethernet1/0\n',
     ' event neighbor-discovery interface regexp .*Ethernet.* cdp add\n',
     ' action 3.0 cli command "interface $_nd_local_intf_name"\n']

Строки, которые находятся в блоке ``if __name__ == '__main__'`` не
выполняются при импорте.

При выводе информации на стандартный поток вывода, проще всего заметить
тот факт, что модуль выполняется при импорте, но гораздо больше проблем
возникает когда, например, надо импортировать функцию из скрипта,
который выполняет подклюнение к сотням устройств. В таком случае, во
время импорта будет выполняться подключение, а только затем сможет
выполниться скрипт, который импортировал другой модуль.

    При создании функции, она не выполняется, поэтому в блок
    ``if __name__ == '__main__'`` выносится код, который вызывает
    функции.
