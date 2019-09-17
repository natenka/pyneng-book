Обработка исключений
--------------------

Посмотрим на пример использования метода execute при возникновении
ошибки.

В таблице switch поле mac должно быть уникальным. И, если попытаться
записать пересекающийся MAC-адрес, возникнет ошибка:

.. code:: python

    In [37]: con = sqlite3.connect('sw_inventory2.db')

    In [38]: query = "INSERT into switch values ('0000.AAAA.DDDD', 'sw7', 'Cisco 2960', 'London, Green Str')"

    In [39]: con.execute(query)
    ------------------------------------------------------------
    IntegrityError             Traceback (most recent call last)
    <ipython-input-56-ad34d83a8a84> in <module>()
    ----> 1 con.execute(query)

    IntegrityError: UNIQUE constraint failed: switch.mac

Соответственно, можно перехватить исключение:

.. code:: python

    In [40]: try:
        ...:     con.execute(query)
        ...: except sqlite3.IntegrityError as e:
        ...:     print("Error occurred: ", e)
        ...:
    Error occurred:  UNIQUE constraint failed: switch.mac

Обратите внимание, что надо перехватывать исключение
sqlite3.IntegrityError, а не IntegrityError.
