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

