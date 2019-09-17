Connection как менеджер контекста
---------------------------------

После выполнения операций изменения должны быть сохранены (надо
выполнить ``commit()``), а затем можно закрыть соединение, если оно
больше не нужно.

Python позволяет использовать объект Connection как менеджер контекста.
В таком случае не нужно явно делать commit.

При этом: 

* при возникновении исключения, транзакция автоматически откатывается 
* если исключения не было, автоматически выполняется commit

Пример использования соединения с базой данных как менеджера контекстов
(create_sw_inventory_ver2.py):


.. literalinclude:: /pyneng-examples-exercises/examples/18_db/create_sw_inventory_ver2.py
  :language: python
  :linenos:

Обратите внимание, что хотя транзакция будет откатываться при
возникновении исключения, само исключение всё равно надо перехватывать.

Для проверки этого функционала надо записать в таблицу данные, в которых
MAC-адрес повторяется. Но прежде, чтобы не повторять части кода, лучше
разнести код в файле create_sw_inventory_ver2.py по функциям (файл
create_sw_inventory_ver2_functions.py):

.. literalinclude:: /pyneng-examples-exercises/examples/18_db/create_sw_inventory_ver2_functions.py
  :language: python
  :linenos:

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

.. literalinclude:: /pyneng-examples-exercises/examples/18_db/create_sw_inventory_ver3.py
  :language: python
  :linenos:

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

.. literalinclude:: /pyneng-examples-exercises/examples/18_db/create_sw_inventory_ver4.py
  :language: python
  :linenos:

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

