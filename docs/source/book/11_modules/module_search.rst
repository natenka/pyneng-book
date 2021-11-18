Пути поиска модулей
-------------------

При импорте модуля, Python сначала ищет модуль в стандартной библиотеке. Если
модуль не найден в стандартной библиотеке, поиск модуля идет в каталогах,
которые указаны в sys.path.

Содержимое sys.path состоит из:

* текущего каталога
* каталогов, которые указаны в переменной PYTHONPATH
* пути по умолчанию (зависят от установки Python)

Пути поиска модулей хранятся в переменной sys.path:

.. code:: python

    In [1]: import sys

    In [2]: sys.path
    Out[2]:
    ['',
     '/usr/local/bin',
     '/usr/local/lib/python36.zip',
     '/usr/local/lib/python3.6',
     '/usr/local/lib/python3.6/lib-dynload',
     '/home/vagrant/.local/lib/python3.6/site-packages',
     '/usr/local/lib/python3.6/site-packages',
     '/usr/local/lib/python3.6/site-packages/IPython/extensions',
     '/home/vagrant/.ipython']

Аналогичный вывод, но внутри виртуального окружения:

.. code:: python

    In [1]: import sys

    In [2]: sys.path
    Out[2]:
    ['/home/vagrant/venv/pyneng-py3-8-0/bin',
     '/home/vagrant/venv/pyneng-py3-8-0/lib/python38.zip',
     '/home/vagrant/venv/pyneng-py3-8-0/lib/python3.8',
     '/home/vagrant/venv/pyneng-py3-8-0/lib/python3.8/lib-dynload',
     '/usr/local/lib/python3.8',
     '',
     '/home/vagrant/venv/pyneng-py3-8-0/lib/python3.8/site-packages',
     '/home/vagrant/venv/pyneng-py3-8-0/lib/python3.8/site-packages/IPython/extensions',
     '/home/vagrant/.ipython']


Добавление своих скриптов в пути поиска модулей
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Добавить свой скрипт в пути поиска модулей нужно в том случае,
если этот скрипт нужно использовать в других скриптах, которые
находятся в разных каталогах.

Для добавления модулей в пути поиска есть несколько вариантов:

1. Переместить скрипт в каталог site-packages
2. Создать специальный файл с расширением pth в каталоге site-packages и написать
   в этом файлы пути поиска модулей

Конкретный путь каталога site-packages зависит от версии Python и того используете ли вы
виртуальное окружение.
Например, в последнем выводе ``sys.path`` путь будет ``'/home/vagrant/venv/pyneng-py3-8-0/lib/python3.8/site-packages'``.
Если переместить туда скрипт, его можно будет импортировать из любого другого скрипта.

Так как переносить файлы не всегда удобно, есть второй вариант - файлы pth.
Для этого варианта надо создать файл с любым именем в каталоге site-packages,
например, ``my_scripts.pth`` и написать в нем пути к нужным скриптам:

::

    /home/vagrant/repos/pyneng/examples/11_modules
    /home/vagrant/repos/pyneng/exercises/09_functions
    /home/vagrant/repos/pyneng/exercises/11_modules
    /home/vagrant/repos/pyneng/exercises/12_userful_modules


.. note::

    Обратите внимание, что тут речь именно об использовании функций какого-то
    вашего скрипта в других скриптах, не о запуске модуля из любого места в 
    файловой системе как утилиты. Это тоже можно сделать, `коротко о том как
    установить скрипт с CLI интерфейсом как утилиту в ОС <https://advpyneng.readthedocs.io/ru/latest/book/04_click/setuptools.html>`__.
    Например, так устанавливалась утилита pyneng.
