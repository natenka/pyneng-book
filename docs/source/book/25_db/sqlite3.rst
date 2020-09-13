.. _sqlite3_index:

Модуль sqlite3
--------------

Для работы с SQLite в Python используется модуль sqlite3.

Объект **Connection** - это подключение к конкретной БД. Можно сказать,
что этот объект представляет БД.

Пример создания подключения:

.. code:: python

    import sqlite3

    connection = sqlite3.connect('dhcp_snooping.db')

После создания соединения надо создать объект Cursor - это основной
способ работы с БД.

Создается курсор из соединения с БД:

.. code:: python

    connection = sqlite3.connect('dhcp_snooping.db')
    cursor = connection.cursor()

.. toctree::
   :maxdepth: 1

   sqlite3_execute
   sqlite3_fetch
   sqlite3_cursor_as_iterator
   sqlite3_connection_without_cursor
   sqlite3_exception
   sqlite3_context_manager
   example_sqlite
