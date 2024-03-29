.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

SQLite
------

`SQLite <http://xgu.ru/wiki/SQLite>`__ — встраиваемая в процесс
реализация SQL-машины.
SQLite часто используется как встроенная СУБД в приложениях.

.. note::

    Слово SQL-сервер здесь не используем, потому что как таковой сервер
    там не нужен — весь функционал, который встраивается в SQL-сервер,
    реализован внутри библиотеки (и, соответственно, внутри программы,
    которая её использует).


SQLite CLI
^^^^^^^^^^

В комплекте поставки SQLite идёт также утилита для работы с SQLite в
командной строке. Утилита представлена в виде исполняемого файла sqlite3
(sqlite3.exe для Windows), и с ее помощью можно вручную выполнять
команды SQL.

С помощью этой утилиты очень удобно проверять правильность команд SQL, а
также в целом знакомиться с языком SQL.

Попробуем с помощью этой утилиты разобраться с базовыми командами SQL,
которые понадобятся для работы с БД.

Для начала разберемся, как создавать БД.

.. note::

    Если вы используете Linux или Mac OS, то, скорее всего, sqlite3
    установлен. Если вы используете Windows, то можно скачать sqlite3
    `тут <http://www.sqlite.org/download.html>`__.

Для того, чтобы создать БД (или открыть уже созданную), надо просто
вызвать sqlite3 таким образом:

::

    $ sqlite3 testDB.db
    SQLite version 3.8.7.1 2014-10-29 13:59:56
    Enter ".help" for usage hints.
    sqlite> 

Внутри sqlite3 можно выполнять команды SQL или так называемые
метакоманды (или dot-команды).


К метакомандам относятся несколько специальных команд для работы с
SQLite. Они относятся только к утилите sqlite3, а не к SQL языку. В
конце этих команд ``;`` ставить не нужно.

Примеры метакоманд: 

* ``.help`` - подсказка со списком всех метакоманд
* ``.exit`` или ``.quit`` - выход из сессии sqlite3 
* ``.databases`` - показывает присоединенные БД 
* ``.tables`` - показывает доступные таблицы

Примеры выполнения:

::

    sqlite> .help
    .backup ?DB? FILE      Backup DB (default "main") to FILE
    .bail ON|OFF           Stop after hitting an error.  Default OFF
    .databases             List names and files of attached databases
    ...

    sqlite> .databases
    seq  name      file                                   
    ---  --------  ----------------------------------
    0    main      /home/nata/py_for_ne/db/db_article/testDB.db              

litecli
^^^^^^^

У стандартного CLI-интерфейса SQLite есть несколько недостатков:

* нет автодополнения команд
* нет подсказок
* не всегда отображается все содержимое столбца

Все эти недостатки исправлены в  `litecli <https://github.com/dbcli/litecli>`__.
Поэтому лучше использовать его. 

Установка litecli:

::

    $ pip install litecli

Открыть базу данных в litecli:

::

    $ litecli example.db
    Version: 1.0.0
    Mail: https://groups.google.com/forum/#!forum/litecli-users
    Github: https://github.com/dbcli/litecli
    example.db>
