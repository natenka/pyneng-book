Пример использования SQLite
---------------------------

В 15 разделе был пример разбора
вывода команды show ip dhcp snooping binding. На выходе мы получили
информацию о параметрах подключенных устройств (interface, IP, MAC,
VLAN).

В таком варианте можно посмотреть только все подключенные устройства к
коммутатору. Если же нужно узнать на основании одного из параметров
другие, то в таком виде это не очень удобно.

Например, если нужно по IP-адресу получить информацию о том, к какому
интерфейсу подключен компьютер, какой у него MAC-адрес и в каком он
VLAN, то по выводу скрипта это сделать не очень просто и, главное, не
очень удобно.

Запишем информацию, полученную из вывода sh ip dhcp snooping binding в
SQLite. Это позволит делать запросы по любому параметру и получать
недостающие.
Для этого примера достаточно создать одну таблицу, где будет храниться
информация.

Определение таблицы прописано в отдельном файле
dhcp_snooping_schema.sql и выглядит так:

.. code:: sql

    create table if not exists dhcp (
        mac          text not NULL primary key,
        ip           text,
        vlan         text,
        interface    text
    );

Для всех полей определен тип данных "текст".

MAC-адрес является первичным ключом нашей таблицы, что вполне логично,
так как MAC-адрес должен быть уникальным.

Кроме того, используется выражение ``create table if not exists`` -
SQLite создаст таблицу только в том случае, если она не существует.

Теперь надо создать файл БД, подключиться к базе данных и создать
таблицу (файл create_sqlite_ver1.py):

.. code:: python

    import sqlite3

    conn = sqlite3.connect('dhcp_snooping.db')

    print('Creating schema...')
    with open('dhcp_snooping_schema.sql', 'r') as f:
        schema = f.read()
        conn.executescript(schema)
    print("Done")

    conn.close()

Комментарии к файлу: 

* при выполнении строки ``conn = sqlite3.connect('dhcp_snooping.db')``: 

  * создается файл dhcp_snooping.db, если его нет 
  * создается объект Connection 

* в БД создается таблица (если ее не было) на основании команд, которые указаны в файле dhcp_snooping_schema.sql: 

  * открывается файл dhcp_snooping_schema.sql 
  * ``schema = f.read()`` - весь файл считывается в одну строку 
  * ``conn.executescript(schema)`` - метод executescript позволяет выполнять команды SQL, которые прописаны в файле

Выполнение скрипта:

::

    $ python create_sqlite_ver1.py
    Creating schema...
    Done

В результате должен быть создан файл БД и таблица dhcp.

Проверить, что таблица создалась, можно с помощью утилиты sqlite3,
которая позволяет выполнять запросы прямо в командной строке.

Список созданных таблиц выводится таким образом:

::

    $ sqlite3 dhcp_snooping.db "SELECT name FROM sqlite_master WHERE type='table'"
    dhcp

Теперь нужно записать информацию из вывода команды sh ip dhcp snooping
binding в таблицу (файл dhcp_snooping.txt):

::

    MacAddress          IpAddress        Lease(sec)  Type           VLAN  Interface
    ------------------  ---------------  ----------  -------------  ----  --------------------
    00:09:BB:3D:D6:58   10.1.10.2        86250       dhcp-snooping   10    FastEthernet0/1
    00:04:A3:3E:5B:69   10.1.5.2         63951       dhcp-snooping   5     FastEthernet0/10
    00:05:B3:7E:9B:60   10.1.5.4         63253       dhcp-snooping   5     FastEthernet0/9
    00:09:BC:3F:A6:50   10.1.10.6        76260       dhcp-snooping   10    FastEthernet0/3
    Total number of bindings: 4

Во второй версии скрипта сначала вывод в файле dhcp_snooping.txt
обрабатывается регулярными выражениями, а затем записи добавляются в БД
(файл create_sqlite_ver2.py):

.. code:: python

    import sqlite3
    import re

    regex = re.compile(r'(\S+) +(\S+) +\d+ +\S+ +(\d+) +(\S+)')

    result = []

    with open('dhcp_snooping.txt') as data:
        for line in data:
            match = regex.search(line)
            if match:
                result.append(match.groups())

    conn = sqlite3.connect('dhcp_snooping.db')

    print('Creating schema...')
    with open('dhcp_snooping_schema.sql', 'r') as f:
        schema = f.read()
        conn.executescript(schema)
    print('Done')

    print('Inserting DHCP Snooping data')

    for row in result:
        try:
            with conn:
                query = '''insert into dhcp (mac, ip, vlan, interface)
                           values (?, ?, ?, ?)'''
                conn.execute(query, row)
        except sqlite3.IntegrityError as e:
            print('Error occured: ', e)

    conn.close()

.. note::

    Пока что файл БД каждый раз надо удалять, так как скрипт пытается
    его создать при каждом запуске.

Комментарии к скрипту: 

* в регулярном выражении, которое проходится по выводу
  команды sh ip dhcp snooping binding, используются не именованные
  группы, как в примере раздела `Регулярные выражения <../14_regex/4a_group_example.md>`__, а нумерованные 

  * группы созданы только для тех элементов, которые нас интересуют 

* result - это список, в котором хранится результат обработки вывода команды 

  * но теперь тут не словари, а кортежи с результатами 
  * это нужно для того, чтобы их можно было сразу передавать на запись в БД 

* Перебираем в полученном списке кортежей элементы 
* В этом скрипте используется еще один вариант записи в БД 

  * строка query описывает запрос. Но вместо значений 
    указываются знаки вопроса. Такой вариант записи запроса
    позволяет динамически подставлять значение полей 
  * затем методу execute передается строка запроса и кортеж row, где находятся значения

Выполняем скрипт:

::

    $ python create_sqlite_ver2.py
    Creating schema...
    Done
    Inserting DHCP Snooping data

Проверим, что данные записались:

::

    $ sqlite3 dhcp_snooping.db "select * from dhcp"
    -- Loading resources from /home/vagrant/.sqliterc

    mac                ip          vlan        interface
    -----------------  ----------  ----------  ---------------
    00:09:BB:3D:D6:58  10.1.10.2   10          FastEthernet0/1
    00:04:A3:3E:5B:69  10.1.5.2    5           FastEthernet0/1
    00:05:B3:7E:9B:60  10.1.5.4    5           FastEthernet0/9
    00:09:BC:3F:A6:50  10.1.10.6   10          FastEthernet0/3

Теперь попробуем запросить по определенному параметру:

::

    $ sqlite3 dhcp_snooping.db "select * from dhcp where ip = '10.1.5.2'"
    -- Loading resources from /home/vagrant/.sqliterc

    mac                ip          vlan        interface
    -----------------  ----------  ----------  ----------------
    00:04:A3:3E:5B:69  10.1.5.2    5           FastEthernet0/10

То есть, теперь на основании одного параметра можно получать остальные.

Переделаем скрипт таким образом, чтобы в нём была проверка на наличие
файла dhcp_snooping.db. Если файл БД есть, то не надо создавать
таблицу, считаем, что она уже создана.

Файл create_sqlite_ver3.py:

.. code:: python

    import os
    import sqlite3
    import re

    data_filename = 'dhcp_snooping.txt'
    db_filename = 'dhcp_snooping.db'
    schema_filename = 'dhcp_snooping_schema.sql'

    regex = re.compile(r'(\S+) +(\S+) +\d+ +\S+ +(\d+) +(\S+)')

    result = []

    with open('dhcp_snooping.txt') as data:
        for line in data:
            match = regex.search(line)
            if match:
                result.append(match.groups())

    db_exists = os.path.exists(db_filename)

    conn = sqlite3.connect(db_filename)

    if not db_exists:
        print('Creating schema...')
        with open(schema_filename, 'r') as f:
            schema = f.read()
        conn.executescript(schema)
        print('Done')
    else:
        print('Database exists, assume dhcp table does, too.')

    print('Inserting DHCP Snooping data')

    for row in result:
        try:
            with conn:
                query = '''insert into dhcp (mac, ip, vlan, interface)
                           values (?, ?, ?, ?)'''
                conn.execute(query, row)
        except sqlite3.IntegrityError as e:
            print('Error occured: ', e)

    conn.close()

Теперь есть проверка наличия файла БД, и файл dhcp_snooping.db будет
создаваться только в том случае, если его нет. Данные также записываются
только в том случае, если не создан файл dhcp_snooping.db.

.. note::

    Разделение процесса создания таблицы и заполнения ее данными
    вынесено в задания к разделу.

Если файла нет (предварительно его удалить):

::

    $ rm dhcp_snooping.db
    $ python create_sqlite_ver3.py
    Creating schema...
    Done
    Inserting DHCP Snooping data

Проверим. В случае, если файл уже есть, но данные не записаны:

::

    $ rm dhcp_snooping.db

    $ python create_sqlite_ver1.py
    Creating schema...
    Done
    $ python create_sqlite_ver3.py
    Database exists, assume dhcp table does, too.
    Inserting DHCP Snooping data

Если есть и БД и данные:

::

    $ python create_sqlite_ver3.py
    Database exists, assume dhcp table does, too.
    Inserting DHCP Snooping data
    Error occurred:  UNIQUE constraint failed: dhcp.mac
    Error occurred:  UNIQUE constraint failed: dhcp.mac
    Error occurred:  UNIQUE constraint failed: dhcp.mac
    Error occurred:  UNIQUE constraint failed: dhcp.mac

Теперь делаем отдельный скрипт, который занимается отправкой запросов в
БД и выводом результатов. Он должен: 

* ожидать от пользователя ввода параметров: 

  * имя параметра 
  * значение параметра 

* делать нормальный вывод данных по запросу

Файл get_data_ver1.py:

.. code:: python

    import sqlite3
    import sys

    db_filename = 'dhcp_snooping.db'

    key, value = sys.argv[1:]
    keys = ['mac', 'ip', 'vlan', 'interface']
    keys.remove(key)

    conn = sqlite3.connect(db_filename)

    #Позволяет далее обращаться к данным в колонках, по имени колонки
    conn.row_factory = sqlite3.Row

    print('\nDetailed information for host(s) with', key, value)
    print('-' * 40)

    query = 'select * from dhcp where {} = ?'.format(key)
    result = conn.execute(query, (value, ))

    for row in result:
        for k in keys:
            print('{:12}: {}'.format(k, row[k]))
        print('-' * 40)

Комментарии к скрипту: 

* из аргументов, которые передали скрипту, считываются параметры key, value 

  * из списка keys удаляется выбранный ключ. Таким образом, 
    в списке остаются только те параметры, которые нужно вывести 

* подключаемся к БД 

  * ``conn.row_factory = sqlite3.Row`` - позволяет далее обращаться к данным в колонках по имени колонки 

* из БД выбираются те строки, в которых ключ равен указанному значению 

  * в SQL значения можно подставлять через знак вопроса, но нельзя подставлять
    имя столбца. Поэтому имя столбца подставляется через форматирование 
    строк, а значение - штатным средством SQL. 
  * Обратите внимание на ``(value,)`` - таким образом передается кортеж с одним элементом 

* Полученная информация выводится на стандартный поток вывода: 
  * перебираем полученные результаты и выводим только те поля, названия
    которых находятся в списке keys

Проверим работу скрипта.

Показать параметры хоста с IP 10.1.10.2:

::

    $ python get_data_ver1.py ip 10.1.10.2

    Detailed information for host(s) with ip 10.1.10.2
    ----------------------------------------
    mac         : 00:09:BB:3D:D6:58
    vlan        : 10
    interface   : FastEthernet0/1
    ----------------------------------------

Показать хосты в VLAN 10:

::

    $ python get_data_ver1.py vlan 10

    Detailed information for host(s) with vlan 10
    ----------------------------------------
    mac         : 00:09:BB:3D:D6:58
    ip          : 10.1.10.2
    interface   : FastEthernet0/1
    ----------------------------------------
    mac         : 00:07:BC:3F:A6:50
    ip          : 10.1.10.6
    interface   : FastEthernet0/3
    ----------------------------------------

Вторая версия скрипта для получения данных с небольшими улучшениями: 

* Вместо форматирования строк используется словарь, в котором описаны
  запросы, соответствующие каждому ключу. 
* Выполняется проверка ключа, который был выбран 
* Для получения заголовков всех столбцов, который
  соответствуют запросу, используется метод keys()

Файл get_data_ver2.py:

.. code:: python

    import sqlite3
    import sys

    db_filename = 'dhcp_snooping.db'

    query_dict = {
        'vlan': 'select mac, ip, interface from dhcp where vlan = ?',
        'mac': 'select vlan, ip, interface from dhcp where mac = ?',
        'ip': 'select vlan, mac, interface from dhcp where ip = ?',
        'interface': 'select vlan, mac, ip from dhcp where interface = ?'
    }

    key, value = sys.argv[1:]
    keys = query_dict.keys()

    if not key in keys:
        print('Enter key from {}'.format(', '.join(keys)))
    else:
        conn = sqlite3.connect(db_filename)
        conn.row_factory = sqlite3.Row

        print('\nDetailed information for host(s) with', key, value)
        print('-' * 40)

        query = query_dict[key]
        result = conn.execute(query, (value, ))

        for row in result:
            for row_name in row.keys():
                print('{:12}: {}'.format(row_name, row[row_name]))
            print('-' * 40)

В этом скрипте есть несколько недостатков: 

* не проверяется количество аргументов, которые передаются скрипту 
* хотелось бы собирать информацию с разных коммутаторов. А для этого надо добавить поле,
  которое указывает, на каком коммутаторе была найдена запись

Кроме того, многое нужно доработать в скрипте, который создает БД и
записывает данные.

Все доработки будут выполняться в заданиях этого раздела.
