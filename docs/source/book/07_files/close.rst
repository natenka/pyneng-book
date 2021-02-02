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

``close``
^^^^^^^^^^^

Метод close встречался в разделе `запись файлов <https://pyneng.readthedocs.io/ru/latest/book/07_files/3_write.html>`__.
Там он был нужен для того, чтобы содержимое файла было записано на диск.

Для этого, в Python есть отдельный метод ``flush``.
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
