Использование модуля sqlite3 без явного создания курсора
--------------------------------------------------------

Методы execute доступны и в объекте Connection, и в объекте Cursor, а
методы fetch доступны только в объекте Cursor.

При использовании методов execute с объектом Connection курсор
возвращается как результат выполнения метода execute. Его можно
использовать как итератор и получать данные без методов fetch.
За счет этого при работе с модулем sqlite3 можно не создавать курсор.

Пример итогового скрипта (файл create_sw_inventory_ver1.py):

.. literalinclude:: /pyneng-examples-exercises/examples/18_db/create_sw_inventory_ver1.py
  :language: python
  :linenos:

Результат выполнения будет таким:

::

    $ python create_sw_inventory_ver1.py
    ('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str')
    ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str')
    ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str')
    ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')

