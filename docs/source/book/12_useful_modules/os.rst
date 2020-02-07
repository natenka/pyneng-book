Модуль os
---------

Модуль ``os`` позволяет работать с файловой системой, с окружением,
управлять процессами.

В этом подразделе рассматриваются лишь несколько полезных возможностей. За более полным
описанием возможностей модуля можно обратиться к
`документации <https://docs.python.org/3/library/os.html>`__ или 
`статье на сайте PyMOTW <https://pymotw.com/3/os/>`__.

Модуль os позволяет создавать каталоги:

.. code:: python

    In [1]: import os

    In [2]: os.mkdir('test')

    In [3]: ls -ls
    total 0
    0 drwxr-xr-x  2 nata  nata  68 Jan 23 18:58 test/

Кроме того, в модуле есть соответствующие проверки на существование.
Например, если попробовать повторно создать каталог, возникнет ошибка:

.. code:: python

    In [4]: os.mkdir('test')
    ---------------------------------------------------------------------------
    FileExistsError                           Traceback (most recent call last)
    <ipython-input-4-cbf3b897c095> in <module>()
    ----> 1 os.mkdir('test')

    FileExistsError: [Errno 17] File exists: 'test'

В таком случае пригодится проверка ``os.path.exists``:

.. code:: python

    In [5]: os.path.exists('test')
    Out[5]: True

    In [6]: if not os.path.exists('test'):
       ...:     os.mkdir('test')
       ...:

Метод listdir позволяет посмотреть содержимое каталога:

.. code:: python

    In [7]: os.listdir('.')
    Out[7]: ['cover3.png', 'dir2', 'dir3', 'README.txt', 'test']

С помощью проверок ``os.path.isdir`` и ``os.path.isfile`` можно получить
отдельно список файлов и список каталогов:

.. code:: python

    In [8]: dirs = [ d for d in os.listdir('.') if os.path.isdir(d)]

    In [9]: dirs
    Out[9]: ['dir2', 'dir3', 'test']

    In [10]: files = [ f for f in os.listdir('.') if os.path.isfile(f)]

    In [11]: files
    Out[11]: ['cover3.png', 'README.txt']

Также в модуле есть отдельные методы для работы с путями:

.. code:: python

    In [12]: file = 'Programming/PyNEng/book/25_additional_info/README.md'

    In [13]: os.path.basename(file)
    Out[13]: 'README.md'

    In [14]: os.path.dirname(file)
    Out[14]: 'Programming/PyNEng/book/25_additional_info'

    In [15]: os.path.split(file)
    Out[15]: ('Programming/PyNEng/book/25_additional_info', 'README.md')

