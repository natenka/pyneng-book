Чтение файлов
-------------

В Python есть несколько методов чтения файла:

* ``read`` - считывает содержимое файла в строку
* ``readline`` - считывает файл построчно
* ``readlines`` - считывает строки файла и создает список из строк

Посмотрим как считывать содержимое файлов, на примере файла r1.txt:

::

    !
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    !
    no ip domain lookup
    !
    ip ssh version 2
    !

``read``
^^^^^^^^^^

Метод ``read`` - считывает весь файл в одну строку.

Пример использования метода ``read``:

.. code:: python

    In [1]: f = open('r1.txt')

    In [2]: f.read()
    Out[2]: '!\nservice timestamps debug datetime msec localtime show-timezone year\nservice timestamps log datetime msec localtime show-timezone year\nservice password-encryption\nservice sequence-numbers\n!\nno ip domain lookup\n!\nip ssh version 2\n!\n'

    In [3]: f.read()
    Out[3]: ''

При повторном чтении файла в 3 строке, отображается пустая строка. Так
происходит из-за того, что при вызове метода ``read``, считывается
весь файл. И после того, как файл был считан, курсор остается в конце
файла. Управлять положением курсора можно с помощью метода ``seek``.

``readline``
^^^^^^^^^^^^^^

Построчно файл можно считать с помощью метода ``readline``:

.. code:: python

    In [4]: f = open('r1.txt')

    In [5]: f.readline()
    Out[5]: '!\n'

    In [6]: f.readline()
    Out[6]: 'service timestamps debug datetime msec localtime show-timezone year\n'

Но чаще всего проще пройтись по объекту file в цикле, не используя
методы ``read...``:

.. code:: python

    In [7]: f = open('r1.txt')

    In [8]: for line in f:
       ...:     print(line)
       ...:
    !

    service timestamps debug datetime msec localtime show-timezone year

    service timestamps log datetime msec localtime show-timezone year

    service password-encryption

    service sequence-numbers

    !

    no ip domain lookup

    !

    ip ssh version 2

    !

``readlines``
^^^^^^^^^^^^^^^

Еще один полезный метод - ``readlines``. Он считывает строки файла в
список:

.. code:: python

    In [9]: f = open('r1.txt')

    In [10]: f.readlines()
    Out[10]:
    ['!\n',
     'service timestamps debug datetime msec localtime show-timezone year\n',
     'service timestamps log datetime msec localtime show-timezone year\n',
     'service password-encryption\n',
     'service sequence-numbers\n',
     '!\n',
     'no ip domain lookup\n',
     '!\n',
     'ip ssh version 2\n',
     '!\n']

Если нужно получить строки файла, но без перевода строки в конце, можно
воспользоваться методом ``split`` и как разделитель, указать символ
``\n``:

::

    In [11]: f = open('r1.txt')

    In [12]: f.read().split('\n')
    Out[12]:
    ['!',
     'service timestamps debug datetime msec localtime show-timezone year',
     'service timestamps log datetime msec localtime show-timezone year',
     'service password-encryption',
     'service sequence-numbers',
     '!',
     'no ip domain lookup',
     '!',
     'ip ssh version 2',
     '!',
     '']

Обратите внимание, что последний элемент списка - пустая строка.

Если перед выполнением ``split``, воспользоваться методом
``rstrip``, список будет без пустой строки в конце:

.. code:: python

    In [13]: f = open('r1.txt')

    In [14]: f.read().rstrip().split('\n')
    Out[14]:
    ['!',
     'service timestamps debug datetime msec localtime show-timezone year',
     'service timestamps log datetime msec localtime show-timezone year',
     'service password-encryption',
     'service sequence-numbers',
     '!',
     'no ip domain lookup',
     '!',
     'ip ssh version 2',
     '!']

``seek``
^^^^^^^^^^

До сих пор, файл каждый раз приходилось открывать заново, чтобы снова
его считать. Так происходит из-за того, что после методов чтения, курсор
находится в конце файла. И повторное чтение возвращает пустую строку.

Чтобы ещё раз считать информацию из файла, нужно воспользоваться методом
``seek``, который перемещает курсор в необходимое положение.

Пример открытия файла и считывания содержимого:

.. code:: python

    In [15]: f = open('r1.txt')

    In [16]: print(f.read())
    !
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    !
    no ip domain lookup
    !
    ip ssh version 2
    !

Если вызывать ещё раз метод ``read``, возвращается пустая строка:

.. code:: python

    In [17]: print(f.read())

Но с помощью метода ``seek`` можно перейти в начало файла (0 означает
начало файла):

.. code:: python

    In [18]: f.seek(0)

После того как с помощью ``seek`` курсор был переведен в начало
файла, можно опять считывать содержимое:

.. code:: python

    In [19]: print(f.read())
    !
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    !
    no ip domain lookup
    !
    ip ssh version 2
    !

