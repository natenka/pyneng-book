Cursor как итератор
-------------------

Если нужно построчно обрабатывать результирующие строки, лучше
использовать курсор как итератор. При этом не нужно использовать методы
fetch.

При использовании методов execute возвращается курсор. А, так как курсор
можно использовать как итератор, можно использовать его, например, в
цикле for:

.. code:: python

    In [34]: result = cursor.execute('select * from switch')

    In [35]: for row in result:
        ...:     print(row)
        ...:
    ('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str')
    ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str')
    ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str')
    ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0001', 'sw5', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0002', 'sw6', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0003', 'sw7', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0004', 'sw8', 'Cisco 3750', 'London, Green Str')

Аналогичный вариант отработает и без присваивания переменной:

.. code:: python

    In [36]: for row in cursor.execute('select * from switch'):
        ...:     print(row)
        ...:
    ('0000.AAAA.CCCC', 'sw1', 'Cisco 3750', 'London, Green Str')
    ('0000.BBBB.CCCC', 'sw2', 'Cisco 3780', 'London, Green Str')
    ('0000.AAAA.DDDD', 'sw3', 'Cisco 2960', 'London, Green Str')
    ('0011.AAAA.CCCC', 'sw4', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0001', 'sw5', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0002', 'sw6', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0003', 'sw7', 'Cisco 3750', 'London, Green Str')
    ('0000.1111.0004', 'sw8', 'Cisco 3750', 'London, Green Str')

