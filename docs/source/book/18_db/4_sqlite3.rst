Модуль sqlite3
--------------

Для работы с SQLite в Python используется модуль sqlite3.

Connection
~~~~~~~~~~

Объект **Connection** - это подключение к конкретной БД. Можно сказать,
что этот объект представляет БД.

Пример создания подключения:

.. code:: python

    import sqlite3

    connection = sqlite3.connect('dhcp_snooping.db')

Cursor
~~~~~~

После создания соединения надо создать объект Cursor - это основной
способ работы с БД.

Создается курсор из соединения с БД:

.. code:: python

    connection = sqlite3.connect('dhcp_snooping.db')
    cursor = connection.cursor()

.. toctree::
   :maxdepth: 1

   4a_sqlite3_execute
   4b_sqlite3_fetch
   4c_sqlite3_cursor_as_iterator
   4d_sqlite3_connection_without_cursor
   4e_sqlit3_exception
   4f_sqlit3_context_manager
