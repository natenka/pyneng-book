Закрытие файлов
---------------

.. note::
    В реальной жизни для закрытия файлов чаще всего используется
    конструкция ``with``. Её намного удобней использовать, чем закрывать
    файл явно. Но, так как в жизни можно встретить и метод ``close``, в
    этом разделе рассматривается как его использовать.

После завершения работы с файлом, его нужно закрыть.
В некоторых случаях Python может самостоятельно закрыть файл.
Но лучше на это не рассчитывать и закрывать файл явно.

``close()``
^^^^^^^^^^^

Метод close встречался в разделе `запись файлов <https://pyneng.readthedocs.io/ru/latest/book/07_files/3_write.html>`__.
Там он был нужен для того, чтобы содержимое файла было записано на диск.

Для этого, в Python есть отдельный метод ``flush()``.
Но так как в примере с записью файлов, не нужно было больше
выполнять никаких операций, файл можно было закрыть.

Откроем файл r1.txt:

.. code:: python

    In [1]: f = open('r1.txt', 'r')

Теперь можно считать содержимое:

.. code:: python

    In [2]: print(f.read())
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

У объекта file есть специальный атрибут ``closed``, который позволяет
проверить, закрыт файл или нет.
Если файл открыт, он возвращает ``False``:

.. code:: python

    In [3]: f.closed
    Out[3]: False

Теперь закрываем файл и снова проверяем ``closed``:

.. code:: python

    In [4]: f.close()

    In [5]: f.closed
    Out[5]: True

Если попробовать прочитать файл, возникнет исключение:

.. code:: python

    In [6]: print(f.read())
    ------------------------------------------------------------------
    ValueError                       Traceback (most recent call last)
    <ipython-input-53-2c962247edc5> in <module>()
    ----> 1 print(f.read())

    ValueError: I/O operation on closed file

Использование ``try/finally`` для работы с файлами
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

С помощью обработки исключений, можно:

-  перехватывать исключения, которые возникают, при попытке прочитать
   несуществующий файл
-  закрывать файл, после всех операций, в блоке ``finally``

Если попытаться открыть для чтения файл, которого не существует,
возникнет такое исключение:

.. code:: python

    In [7]: f = open('r3.txt', 'r')
    ---------------------------------------------------------------------------
    IOError                                   Traceback (most recent call last)
    <ipython-input-54-1a33581ca641> in <module>()
    ----> 1 f = open('r3.txt', 'r')

    IOError: [Errno 2] No such file or directory: 'r3.txt'

С помощью конструкции ``try/except``, можно перехватить это исключение и
вывести своё сообщение:

.. code:: python

    In [8]: try:
      ....:     f = open('r3.txt', 'r')
      ....: except IOError:
      ....:     print('No such file')
      ....:
    No such file

А с помощью части ``finally``, можно закрыть файл, после всех операций:

.. code:: python

    In [9]: try:
      ....:     f = open('r1.txt', 'r')
      ....:     print(f.read())
      ....: except IOError:
      ....:     print('No such file')
      ....: finally:
      ....:     f.close()
      ....:
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

    In [10]: f.closed
    Out[10]: True

