.. _debug:

Отладка кода
============

Отладка кода это сложный процесс, который даже с опытом, остается сложным.
Упростить его можно изучив доступные инструменты для отладки.

Глобально, можно сказать, есть два варианта отладки:

* добавляем print в каком-то виде
* используем отладчик

Оба варианта можно использовать и конечно при использовании отладчика намного
больше возможностей, но при этом надо потратить время на изучение самого
отладчика.

Также хочется выделить отдельно отладку для изучения Python. То есть когда вы
используете какой-то отладчик и/или print не потому что код не работает, а
потому что вы пытаетесь понять, что  происходит в коде в тот или иной момент.


Для изучения Python подходят:

* `сайт pythontutor <https://pythontutor.com/>`__
* отладчики в редакторах для начинающих: Mu, Thonny
* технически также подходит print
* если опыт в программировании уже есть, скорее всего, также подойдут отладчики в IDE

Отладчик
--------

Отладчик (debugger) это отдельный модуль, софт или часть редактора/IDE, которая позволяет
делать отладку кода.

Примеры отладчиков и редакторов с отладчиками:

* `сайт pythontutor <https://pythontutor.com/>`__
* `pdb <https://docs.python.org/3/library/pdb.html>`__ - модуль стандартной библиотеки Python (есть много разновидностей pdbr, ipdb, pdbpp)
* `Mu <https://codewith.mu/>`__
* `Thonny <https://thonny.org/>`__
* `PyCharm <https://www.jetbrains.com/pycharm/>`__
* `VS Code <https://code.visualstudio.com/>`__


Отладка с помощью print и разновидностей
----------------------------------------

Отладка с помощью print обычно заключается в том, что в код, в непонятных
местах или перед строкой с ошибкой, добавляется print в каком-то виде.

print vs pprint vs ``print(f"{value=}")``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Обычный print с выводом значений переменных не всегда лучший выбор, так как,
например, print одинаково выводит строку ``"100"`` и число ``100``.


.. code:: python

    In [1]: print("100")
    100

    In [2]: print(100)
    100

Тут может быть удобнее pprint, который выводит строки с кавычками, а числа нет:

.. code:: python

    In [3]: from pprint import pprint

    In [5]: pprint("100")
    '100'

    In [6]: pprint(100)
    100

Также pprint удобен для вывода более сложных строк, так как он показывает
специальные символы:

.. code:: python

    In [7]: line = "\nline1\t\nline2\r\n"

    In [8]: print(line)

    line1
    line2


    In [9]: pprint(line)
    '\nline1\t\nline2\r\n'

.. note::

    Технически можно вывести такую же строку с print, если использовать repr
    вместе с print:

    .. code:: python

        In [11]: print(repr(line))
        '\n\nline1\t\nline2\r\n'


Минус pprint в том, что он может выводить только одно значение, то есть нельзя
сделать как с print, вывод сразу нескольких переменных.
Плюс в том, что pprint также умеет выводит более сложные структуры данных с
понятным форматированием, а также дает возможность указывать "глубину" данных,
которую надо показывать.  Подробнее о :ref:`pprint`.

Также, начиная с версии 3.8, в Python появилась специальная разновидность
вывода в f-строках, именно для отладки - ``f"{var=}"``:

.. code:: python

    In [13]: line = "\nline1\t\nline2"

    In [14]: item = "100"

    In [15]: print(f"{line=} {item=}")
    line='\nline1\t\nline2' item='100'

    In [16]: for i in range(5):
        ...:     print(f"{i=}")
        ...:
    i=0
    i=1
    i=2
    i=3
    i=4

Этот вариант с одной стороны, позволяет отображать сколько угодно значений,
плюс автоматически добавляет имя переменной к выводу, с другой, выводит как и
pprint, строки со специальными символами и кавычками.


locals
-------

Функция locals показывает все локальные переменные. Если в коде нет функций,
это будут все глобальные переменные, если сделать вывод внутри функции, только
переменные этой функции.

Пример кода:

.. code:: python

    from pprint import pprint

    item = "100"
    line = "\nline1\n\tline2"

    pprint(locals())

Вывод locals

.. code:: python

    {'__annotations__': {},
     '__builtins__': <module 'builtins' (built-in)>,
     '__cached__': None,
     '__doc__': None,
     '__file__': '/home/user/repos/pyneng-14/pyneng-course-tasks/exercises/15_module_re/code.py',
     '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0xb73f30d0>,
     '__name__': '__main__',
     '__package__': None,
     '__spec__': None,
     'item': '100',
     'line': '\nline1\n\tline2',
     'pprint': <function pprint at 0xb7253808>}


rich.inspect
------------

Rich это сторонний модуль, в котором есть много возможностей для вывода
информации терминале: красивые таблицы, вывод в цвете, progress bar и другие
возможности.
Одна из удобных возможностей rich - функция inspect. Она выводит информацию про
указанный объект, его методы и атрибуты.

Установка rich

::

    pip install rich

Пример использования rich.inspect со списком:

.. code:: python

    In [10]: from rich import inspect

    In [11]: items = [10, 20, 30, 40]

    In [12]: inspect(items)
    ╭─────────────────────── <class 'list'> ───────────────────────╮
    │ Built-in mutable sequence.                                   │
    │                                                              │
    │ ╭──────────────────────────────────────────────────────────╮ │
    │ │ [10, 20, 30, 40]                                         │ │
    │ ╰──────────────────────────────────────────────────────────╯ │
    │                                                              │
    │ 36 attribute(s) not shown. Run inspect(inspect) for options. │
    ╰──────────────────────────────────────────────────────────────╯

    In [13]: inspect(items, methods=True)
    ╭──────────────────────────────────── <class 'list'> ────────────────────────────────────╮
    │ Built-in mutable sequence.                                                             │
    │                                                                                        │
    │ ╭────────────────────────────────────────────────────────────────────────────────────╮ │
    │ │ [10, 20, 30, 40]                                                                   │ │
    │ ╰────────────────────────────────────────────────────────────────────────────────────╯ │
    │                                                                                        │
    │  append = def append(object, /): Append object to the end of the list.                 │
    │   clear = def clear(): Remove all items from list.                                     │
    │    copy = def copy(): Return a shallow copy of the list.                               │
    │   count = def count(value, /): Return number of occurrences of value.                  │
    │  extend = def extend(iterable, /): Extend list by appending elements from the          │
    │           iterable.                                                                    │
    │   index = def index(value, start=0, stop=2147483647, /): Return first index of value.  │
    │  insert = def insert(index, object, /): Insert object before index.                    │
    │     pop = def pop(index=-1, /): Remove and return item at index (default last).        │
    │  remove = def remove(value, /): Remove first occurrence of value.                      │
    │ reverse = def reverse(): Reverse *IN PLACE*.                                           │
    │    sort = def sort(*, key=None, reverse=False): Sort the list in ascending order and   │
    │           return None.                                                                 │
    ╰────────────────────────────────────────────────────────────────────────────────────────╯

Пример использования rich.inspect с файлом:

.. code:: python

    In [19]: f = open("code.py")

    In [20]: inspect(f)
    ╭──────────────────── <class '_io.TextIOWrapper'> ─────────────────────╮
    │ Character and line based layer over a BufferedIOBase object, buffer. │
    │                                                                      │
    │ ╭──────────────────────────────────────────────────────────────────╮ │
    │ │ <_io.TextIOWrapper name='code.py' mode='r' encoding='UTF-8'>     │ │
    │ ╰──────────────────────────────────────────────────────────────────╯ │
    │                                                                      │
    │         buffer = <_io.BufferedReader name='code.py'>                 │
    │         closed = False                                               │
    │       encoding = 'UTF-8'                                             │
    │         errors = 'strict'                                            │
    │ line_buffering = False                                               │
    │           mode = 'r'                                                 │
    │           name = 'code.py'                                           │
    │       newlines = None                                                │
    │  write_through = False                                               │
    ╰──────────────────────────────────────────────────────────────────────╯

    In [21]: inspect(f, methods=True)
    ╭───────────────────────────── <class '_io.TextIOWrapper'> ──────────────────────────────╮
    │ Character and line based layer over a BufferedIOBase object, buffer.                   │
    │                                                                                        │
    │ ╭────────────────────────────────────────────────────────────────────────────────────╮ │
    │ │ <_io.TextIOWrapper name='code.py' mode='r' encoding='UTF-8'>                       │ │
    │ ╰────────────────────────────────────────────────────────────────────────────────────╯ │
    │                                                                                        │
    │         buffer = <_io.BufferedReader name='code.py'>                                   │
    │         closed = False                                                                 │
    │       encoding = 'UTF-8'                                                               │
    │         errors = 'strict'                                                              │
    │ line_buffering = False                                                                 │
    │           mode = 'r'                                                                   │
    │           name = 'code.py'                                                             │
    │       newlines = None                                                                  │
    │  write_through = False                                                                 │
    │          close = def close(): Flush and close the IO object.                           │
    │         detach = def detach(): Separate the underlying buffer from the TextIOBase and  │
    │                  return it.                                                            │
    │         fileno = def fileno(): Returns underlying file descriptor if one exists.       │
    │          flush = def flush(): Flush write buffers, if applicable.                      │
    │         isatty = def isatty(): Return whether this is an 'interactive' stream.         │
    │           read = def read(size=-1, /): Read at most n characters from stream.          │
    │       readable = def readable(): Return whether object was opened for reading.         │
    │       readline = def readline(size=-1, /): Read until newline or EOF.                  │
    │      readlines = def readlines(hint=-1, /): Return a list of lines from the stream.    │
    │    reconfigure = def reconfigure(*, encoding=None, errors=None, newline=None,          │
    │                  line_buffering=None, write_through=None): Reconfigure the text stream │
    │                  with new parameters.                                                  │
    │           seek = def seek(cookie, whence=0, /): Change stream position.                │
    │       seekable = def seekable(): Return whether object supports random access.         │
    │           tell = def tell(): Return current stream position.                           │
    │       truncate = def truncate(pos=None, /): Truncate file to size bytes.               │
    │       writable = def writable(): Return whether object was opened for writing.         │
    │          write = def write(text, /):                                                   │
    │                  Write string to stream.                                               │
    │                  Returns the number of characters written (which is always equal to    │
    │                  the length of the string).                                            │
    │     writelines = def writelines(lines, /): Write a list of lines to stream.            │
    ╰────────────────────────────────────────────────────────────────────────────────────────╯

rich.traceback
--------------

Еще одна полезная возможность rich - красивый traceback.

Код с ошибкой:

.. code:: python

    vlans = ["1", "2", "3", "test", "4", "5", "switchport allowed vlans add"]

    vlans_list = []
    for vl in vlans:
        new_vl = int(vl)
        vlans_list.append(new_vl)

    print(vlans_list)


Стандартный traceback для кода:

::

    $ python basics_debug_05_rich_traceback.py
    Traceback (most recent call last):
      File "/examples/basics_debug_05_rich_traceback.py", line 11, in <module>
        new_vl = int(vl)
    ValueError: invalid literal for int() with base 10: 'test'


С использованием rich (часть вывода locals сокращена):

.. code:: python

    $ python basics_debug_05_rich_traceback.py
    ╭──────────────────── Traceback (most recent call last) ────────────────────╮
    │ /examples/basics_debug_05_rich_traceback.py:11 in <module>                │
    │                                                                           │
    │    7                                                                      │
    │    8                                                                      │
    │    9 vlans_list = []                                                      │
    │   10 for vl in vlans:                                                     │
    │ ❱ 11 │   new_vl = int(vl)                                                 │
    │   12 │   vlans_list.append(new_vl)                                        │
    │   13                                                                      │
    │   14 print(vlans_list)                                                    │
    │   15                                                                      │
    │                                                                           │
    │ ╭─────────────────────────────── locals ────────────────────────────────╮ │
    │ │          new_vl = 3                                                   │ │
    │ │              vl = 'test'                                              │ │
    │ │           vlans = [                                                   │ │
    │ │                   │   '1',                                            │ │
    │ │                   │   '2',                                            │ │
    │ │                   │   '3',                                            │ │
    │ │                   │   'test',                                         │ │
    │ │                   │   '4',                                            │ │
    │ │                   │   '5',                                            │ │
    │ │                   │   'switchport allowed vlans add'                  │ │
    │ │                   ]                                                   │ │
    │ │      vlans_list = [1, 2, 3]                                           │ │
    │ ╰───────────────────────────────────────────────────────────────────────╯ │
    ╰───────────────────────────────────────────────────────────────────────────╯
    ValueError: invalid literal for int() with base 10: 'test'

Получить такой вывод можно добавив в файл с кодом такие строки:

.. code:: python

    from rich.traceback import install
    install(show_locals=True, extra_lines=5)

Полный код

.. code:: python

    from rich.traceback import install
    install(show_locals=True, extra_lines=5)

    vlans = ["1", "2", "3", "test", "4", "5", "switchport allowed vlans add"]

    vlans_list = []
    for vl in vlans:
        new_vl = int(vl)
        vlans_list.append(new_vl)

    print(vlans_list)

И, если такой traceback понравится, можно сделать так, чтобы он использовался по умолчанию.
Для этого надо создать файл sitecustomize.py в каталоге site-packages с таким содержимым:

.. code:: python

    from rich.traceback import install
    install(show_locals=True, extra_lines=5)

Как понять какой каталог site-packages использовать:

::

    $ python -m site
    sys.path = [
        '/home/user/repos/examples/',
        '/usr/local/lib/python310.zip',
        '/usr/local/lib/python3.10',
        '/usr/local/lib/python3.10/lib-dynload',
        '/home/user/venv/pyneng-py3-10-0/lib/python3.10/site-packages',
    ]
    USER_BASE: '/home/user/.local' (exists)
    USER_SITE: '/home/user/.local/lib/python3.10/site-packages' (doesn't exist)

Полный путь к site-packages показан в sys.path, в данном случае это путь:

::

    '/home/user/venv/pyneng-py3-10-0/lib/python3.10/site-packages',

Встроенный отладчик pdb
-----------------------

Как запустить pdb

::

    python -m pdb script.py

Для выхода из pdb используется команда ``q``.

В любой момент можно перезапустить скрипт, без потери breakpoint, с
помощью команды ``run``.

Базовые команды передвижения по программе
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  n (next) - выполнить все до следующей строки. Эта команда не заходит
   в функции, которые вызываются в строке
-  s (step) - выполнить текущую строку, остановиться как можно раньше.
   Эта команда заходит в функции, которые вызываются в строке
-  c (continue) - выполнить все до breakpoint. Также полезна, когда
   скрипт отрабатывает с исключением, позволяет дойти до строки, где
   возникло исключение

Контекст в коде, переменные
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  l (list) - показывает следующую строку, которая будет выполняться и 5
   строк до и после нее. При добавлении диапазона показывает указанные
   строки, например, ``list 1, 20`` покажет код с 1 по 20 строку
-  ll (longlist) - показывает весь метод или функцию в котором мы
   находимся
-  a (args) - показывает аргументы функции (или метода) и их значения.
   Работает только внутри функции
-  p - показывает значение переменной, работает как print. Синтаксис
   ``p vara``, где vara имя переменной
-  pp - показывает значение переменной, работает как pprint. Синтаксис
   ``pp vara``, где vara имя переменной

Выполнение Python команд в pdb
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Любую команду можно выполнить указав ``!`` перед ней:

::

    !vara = 55
    !result.append(vara)

Таким образом можно пробовать выполнить какие-то действия в текущем
контексте программы, изменить значения переменных.

Также можно перейти в интерпретатор python из текущего контекста. Для
этого используется команда interact:

::

    (Pdb) interact
    *interactive*
    >>> print(cfg)
    <_io.TextIOWrapper name='sh_cdp_n_sw1.txt' mode='r' encoding='UTF-8'>
    >>> cfg.closed
    False
    >>>
    >>> data = ['1','2','3']
    >>> print(','.join(data))
    1,2,3
    >>>
    now exiting InteractiveConsole...
    (Pdb)

Для выхода из интерпретатор используется команда ``Ctrl-d``.

Дополнительные команды по передвижению
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  until - выполнить все до указанной строки. Синтаксис ``until 15``,
   где 15 номер строки
-  return - выполняется внутри функции и выполняет все до return
-  u (up) - передвинутся на один уровень выше в стеке вызовов. Например,
   если мы по цепоцке переходили в один вызов функции, затем в друго,
   чтобы вернуться назад надо использовать up
-  d (down) - передвинутся на один уровень ниже в стеке вызовов

Breakpoints
~~~~~~~~~~

-  b (break) - команда для установки breakpoint

Если команда указывается с аргументом, например, ``break 12`` или
``break check_ip``, устанавливается breakpoint. Без аргументов, команда
показывает все установленные breakpoint.

Удаление breakpoint под номером 1:

::

    clear 1

Базовые варианты установки breakpoint
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Установить breakpoint в строке 12:

::

    break 12

Установить breakpoint в первой строке функции check\_ip:

::

    break check_ip

Breakpoint с условием
^^^^^^^^^^^^^^^^^^^^

Сделать breakpoint в строке 12, если значение переменной num будет
больше 10:

::

    break 12, num > 10

Привязка команд к breakpoint
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Создаем breakpoint (предполагаем, что он первый, поэтому его номер будет
1):

::

    break 12

Добавляем команды, которые будут выполняться каждый раз, когда попадаем
на breakpoint (var1, var2, result\_dict должны быть заменены на ваши
переменные)

::

    commands 1
     pp var1
     pp var2
     pp result_dict
    end

pdbr: rich + pdb
----------------

Модуль pdbr это одна из разновидностей pdb, которая добавляет несколько
полезных команд для начинающих.

Установка pdbr:

::

    pip install pdbr


Работают все команды pdb, но также добавлены несколько полезных команд:

* v (vars) - таблица локальных переменных
* vt (varstree) - дерево локальных переменных
* inspect - вывод rich.inspect для объекта


.. code:: python

    (Pdbr) vars
                                     List of local variables
                 ╷                                                          ╷
      Variable   │ Value                                                    │ Type
    ╶────────────┼──────────────────────────────────────────────────────────┼────────────────╴
      vlans      │ ['1', '2', '3', 'test', '4', '5', 'switchport allowed    │ <class 'list'>
                 │ vlans add']                                              │
      vlans_list │ [1, 2, 3]                                                │ <class 'list'>
      vl         │ test                                                     │ <class 'str'>
      new_vl     │ 3                                                        │ <class 'int'>
                 ╵                                                          ╵
                 ╵                                                          ╵
    (Pdbr) varstree
    Variables
    ├── <class 'int'>
    │   └── new_vl: 3
    ├── <class 'list'>
    │   ├── vlans: ['1', '2', '3', 'test', '4', '5', 'switchport allowed vlans add']
    │   └── vlans_list: [1, 2, 3]
    └── <class 'str'>
        └── vl: test

    (Pdbr) inspect vlans
    ╭──────────────────────────────────── <class 'list'> ────────────────────────────────────╮
    │ Built-in mutable sequence.                                                             │
    │                                                                                        │
    │ ╭────────────────────────────────────────────────────────────────────────────────────╮ │
    │ │ ['1', '2', '3', 'test', '4', '5', 'switchport allowed vlans add']                  │ │
    │ ╰────────────────────────────────────────────────────────────────────────────────────╯ │
    │                                                                                        │
    │  append = def append(object, /): Append object to the end of the list.                 │
    │   clear = def clear(): Remove all items from list.                                     │
    │    copy = def copy(): Return a shallow copy of the list.                               │
    │   count = def count(value, /): Return number of occurrences of value.                  │
    │  extend = def extend(iterable, /): Extend list by appending elements from the          │
    │           iterable.                                                                    │
    │   index = def index(value, start=0, stop=2147483647, /): Return first index of value.  │
    │  insert = def insert(index, object, /): Insert object before index.                    │
    │     pop = def pop(index=-1, /): Remove and return item at index (default last).        │
    │  remove = def remove(value, /): Remove first occurrence of value.                      │
    │ reverse = def reverse(): Reverse *IN PLACE*.                                           │
    │    sort = def sort(*, key=None, reverse=False): Sort the list in ascending order and   │
    │           return None.                                                                 │
    ╰────────────────────────────────────────────────────────────────────────────────────────╯

