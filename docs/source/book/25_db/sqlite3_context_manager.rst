Connection как менеджер контекста
---------------------------------

После выполнения операций изменения должны быть сохранены (надо
выполнить ``commit``, а затем можно закрыть соединение, если оно
больше не нужно.

Python позволяет использовать объект Connection как менеджер контекста.
В таком случае не нужно явно делать commit.

При этом: 

* при возникновении исключения, транзакция автоматически откатывается 
* если исключения не было, автоматически выполняется commit

Пример использования соединения с базой данных как менеджера контекстов
(create_sw_inventory_ver2.py):


.. code:: python

    import sqlite3


    data = [('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str'),
            ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str'),
            ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str'),
            ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')]


    con = sqlite3.connect('sw_inventory3.db')
    con.execute('''create table switch
                   (mac text not NULL primary key, hostname text, model text, location text)'''
                )

    try:
        with con:
            query = 'INSERT into switch values (?, ?, ?, ?)'
            con.executemany(query, data)

    except sqlite3.IntegrityError as e:
        print('Возникла ошибка: ', e)

    for row in con.execute('select * from switch'):
        print(row)

    con.close()

Обратите внимание, что хотя транзакция будет откатываться при
возникновении исключения, само исключение всё равно надо перехватывать.

Для проверки этого функционала надо записать в таблицу данные, в которых
MAC-адрес повторяется. Но прежде, чтобы не повторять части кода, лучше
разнести код в файле create_sw_inventory_ver2.py по функциям (файл
create_sw_inventory_ver2_functions.py):

.. code:: python

    from pprint import pprint
    import sqlite3


    data = [('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str'),
            ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str'),
            ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str'),
            ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')]


    def create_connection(db_name):
        '''
        Функция создает соединение с БД db_name
        и возвращает его
        '''
        connection = sqlite3.connect(db_name)
        return connection


    def write_data_to_db(connection, query, data):
        '''
        Функция ожидает аргументы:
         * connection - соединение с БД
         * query - запрос, который нужно выполнить
         * data - данные, которые надо передать в виде списка кортежей

        Функция пытается записать все данные из списка data.
        Если данные удалось записать успешно, изменения сохраняются в БД
        и функция возвращает True.
        Если в процессе записи возникла ошибка, транзакция откатывается
        и функция возвращает False.
        '''
        try:
            with connection:
                connection.executemany(query, data)
        except sqlite3.IntegrityError as e:
            print('Возникла ошибка: ', e)
            return False
        else:
            print('Запись данных прошла успешно')
            return True


    def get_all_from_db(connection, query):
        '''
        Функция ожидает аргументы:
         * connection - соединение с БД
         * query - запрос, который нужно выполнить

        Функция возвращает данные полученные из БД.
        '''
        result = [row for row in connection.execute(query)]
        return result


    if __name__ == '__main__':
        con = create_connection('sw_inventory3.db')

        print('Создание таблицы...')
        schema = '''create table switch
                    (mac text primary key, hostname text, model text, location text)'''
        con.execute(schema)

        query_insert = 'INSERT into switch values (?, ?, ?, ?)'
        query_get_all = 'SELECT * from switch'

        print('Запись данных в БД:')
        pprint(data)
        write_data_to_db(con, query_insert, data)
        print('\nПроверка содержимого БД')
        pprint(get_all_from_db(con, query_get_all))

        con.close()

Результат выполнения скрипта выглядит так:

::

    $ python create_sw_inventory_ver2_functions.py
    Создание таблицы...
    Запись данных в БД:
    [('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str'),
     ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str'),
     ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str'),
     ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')]
    Запись данных прошла успешно

    Проверка содержимого БД
    [('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str'),
     ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str'),
     ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str'),
     ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')]

Теперь проверим, как функция write_data_to_db отработает при наличии
одинаковых MAC-адресов в данных.

В файле create_sw_inventory_ver3.py используются функции из файла
create_sw_inventory_ver2_functions.py и подразумевается, что скрипт
будет запускаться после записи предыдущих данных:

.. code:: python

    from pprint import pprint
    import sqlite3
    import create_sw_inventory_ver2_functions as dbf

    #MAC-адрес sw7 совпадает с MAC-адресом коммутатора sw3 в списке data
    data2 = [('0055.AAAA.CCCC', 'sw5', 'Cisco 3750', 'London, Green Str'),
             ('0066.BBBB.CCCC', 'sw6', 'Cisco 3780', 'London, Green Str'),
             ('0000.AAAA.DDDD', 'sw7', 'Cisco 2960', 'London, Green Str'),
             ('0088.AAAA.CCCC', 'sw8', 'Cisco 3750', 'London, Green Str')]

    con = dbf.create_connection('sw_inventory3.db')

    query_insert = "INSERT into switch values (?, ?, ?, ?)"
    query_get_all = "SELECT * from switch"

    print("\nПроверка текущего содержимого БД")
    pprint(dbf.get_all_from_db(con, query_get_all))

    print('-' * 60)
    print("Попытка записать данные с повторяющимся MAC-адресом:")
    pprint(data2)
    dbf.write_data_to_db(con, query_insert, data2)
    print("\nПроверка содержимого БД")
    pprint(dbf.get_all_from_db(con, query_get_all))

    con.close()

В списке data2 у коммутатора sw7 MAC-адрес совпадает с уже существующим
в БД коммутатором sw3.

Результат выполнения скрипта:

::

    $ python create_sw_inventory_ver3.py

    Проверка текущего содержимого БД
    [('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str'),
     ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str'),
     ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str'),
     ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')]
    ------------------------------------------------------------
    Попытка записать данные с повторяющимся MAC-адресом:
    [('0055.AAAA.CCCC', 'sw5', 'Cisco 3750', 'London, Green Str'),
     ('0066.BBBB.CCCC', 'sw6', 'Cisco 3780', 'London, Green Str'),
     ('0000.AAAA.DDDD', 'sw7', 'Cisco 2960', 'London, Green Str'),
     ('0088.AAAA.CCCC', 'sw8', 'Cisco 3750', 'London, Green Str')]
    Error occurred:  UNIQUE constraint failed: switch.mac

    Проверка содержимого БД
    [('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str'),
     ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str'),
     ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str'),
     ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')]

Обратите внимание, что содержимое таблицы switch до и после добавления
информации одинаково. Это значит, что не записалась ни одна строка из
списка data2.

Так получилось из-за того, что используется метод executemany, и в
пределах одной транзакции мы пытаемся записать все 4 строки. Если
возникает ошибка с одной из них - откатываются все изменения.

Иногда это именно то поведение, которое нужно. Если же надо, чтобы
игнорировались только строки с ошибками, надо использовать метод execute
и записывать каждую строку отдельно.

В файле create_sw_inventory_ver4.py создана функция
write_rows_to_db, которая уже по очереди пишет данные и, если
возникла ошибка, то только изменения для конкретных данных откатываются:

.. code:: python

    from pprint import pprint
    import sqlite3
    import create_sw_inventory_ver2_functions as dbf

    #MAC-адрес sw7 совпадает с MAC-адресом коммутатора sw3 в списке data
    data2 = [('0055.AAAA.CCCC', 'sw5', 'Cisco 3750', 'London, Green Str'),
             ('0066.BBBB.CCCC', 'sw6', 'Cisco 3780', 'London, Green Str'),
             ('0000.AAAA.DDDD', 'sw7', 'Cisco 2960', 'London, Green Str'),
             ('0088.AAAA.CCCC', 'sw8', 'Cisco 3750', 'London, Green Str')]


    def write_rows_to_db(connection, query, data, verbose=False):
        '''
        Функция ожидает аргументы:
         * connection - соединение с БД
         * query - запрос, который нужно выполнить
         * data - данные, которые надо передать в виде списка кортежей

        Функция пытается записать поочереди кортежи из списка data.
        Если кортеж удалось записать успешно, изменения сохраняются в БД.
        Если в процессе записи кортежа возникла ошибка, транзакция откатывается.

        Флаг verbose контролирует то, будут ли выведены сообщения об удачной
        или неудачной записи кортежа.
        '''
        for row in data:
            try:
                with connection:
                    connection.execute(query, row)
            except sqlite3.IntegrityError as e:
                if verbose:
                    print("При записи данных '{}' возникла ошибка".format(
                        ', '.join(row), e))
            else:
                if verbose:
                    print("Запись данных '{}' прошла успешно".format(
                        ', '.join(row)))


    con = dbf.create_connection('sw_inventory3.db')

    query_insert = 'INSERT into switch values (?, ?, ?, ?)'
    query_get_all = 'SELECT * from switch'

    print('\nПроверка текущего содержимого БД')
    pprint(dbf.get_all_from_db(con, query_get_all))

    print('-' * 60)
    print('Попытка записать данные с повторяющимся MAC-адресом:')
    pprint(data2)
    write_rows_to_db(con, query_insert, data2, verbose=True)
    print('\nПроверка содержимого БД')
    pprint(dbf.get_all_from_db(con, query_get_all))

    con.close()

Теперь результат выполнения будет таким (пропущен только sw7):

::

    $ python create_sw_inventory_ver4.py

    Проверка текущего содержимого БД
    [('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str'),
     ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str'),
     ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str'),
     ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')]
    ------------------------------------------------------------
    Попытка записать данные с повторяющимся MAC-адресом:
    [('0055.AAAA.CCCC', 'sw5', 'Cisco 3750', 'London, Green Str'),
     ('0066.BBBB.CCCC', 'sw6', 'Cisco 3780', 'London, Green Str'),
     ('0000.AAAA.DDDD', 'sw7', 'Cisco 2960', 'London, Green Str'),
     ('0088.AAAA.CCCC', 'sw8', 'Cisco 3750', 'London, Green Str')]
    Запись данных "0055.AAAA.CCCC, sw5, Cisco 3750, London, Green Str" прошла успешно
    Запись данных "0066.BBBB.CCCC, sw6, Cisco 3780, London, Green Str" прошла успешно
    При записи данных "0000.AAAA.DDDD, sw7, Cisco 2960, London, Green Str" возникла ошибка
    Запись данных "0088.AAAA.CCCC, sw8, Cisco 3750, London, Green Str" прошла успешно

    Проверка содержимого БД
    [('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str'),
     ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str'),
     ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str'),
     ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str'),
     ('0055.AAAA.CCCC', 'sw5', 'Cisco 3750', 'London, Green Str'),
     ('0066.BBBB.CCCC', 'sw6', 'Cisco 3780', 'London, Green Str'),
     ('0088.AAAA.CCCC', 'sw8', 'Cisco 3750', 'London, Green Str')]

