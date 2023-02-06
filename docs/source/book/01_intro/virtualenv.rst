.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

Виртуальные окружения
---------------------

Виртуальные окружения:

-  позволяют изолировать различные проекты друг от друга;
-  пакеты, которые нужны разным проектам, находятся в разных местах –
   если, например, в одном проекте требуется пакет версии 1.0, а в
   другом проекте требуется тот же пакет, но версии 3.1, то они не будут
   мешать друг другу;
-  пакеты, которые установлены в виртуальных окружениях, не перебивают
   глобальные пакеты.


Встроенный модуль venv
^^^^^^^^^^^^^^^^^^^^^^

Начиная с версии 3.5, в Python рекомендуется использовать модуль venv
для создания виртуальных окружений:

::

    $ python3.11 -m venv ~/venv/pyneng

Вместо python3.11 может использоваться python или python3, в зависимости
от того, как установлен Python 3.11. Эта команда создаёт указанный
каталог и все необходимые каталоги внутри него, если они не были
созданы.

Команда создаёт следующую структуру каталогов:

::

    $ ls -ls ~/venv/pyneng
    total 16
    4 drwxr-xr-x 2 vagrant vagrant 4096 Aug 21 14:50 bin
    4 drwxr-xr-x 2 vagrant vagrant 4096 Aug 21 14:50 include
    4 drwxr-xr-x 3 vagrant vagrant 4096 Aug 21 14:50 lib
    4 -rw-r--r-- 1 vagrant vagrant   75 Aug 21 14:50 pyvenv.cfg

Для перехода в виртуальное окружение надо выполнить команду:

::

    $ source ~/venv/pyneng/bin/activate

Для выхода из виртуального окружения используется команда deactivate:

::

    $ deactivate

Подробнее о модуле venv в
`документации <https://docs.python.org/3/library/venv.html#module-venv>`__.

Установка пакетов
^^^^^^^^^^^^^^^^^

Например, установим в виртуальном окружении пакет simplejson.

::

    (pyneng)$ pip install simplejson
    ...
    Successfully installed simplejson
    Cleaning up...

Если перейти в интерпретатор Python и импортировать simplejson, то он доступен
и никаких ошибок нет:

::

    (pyneng)$ python
    >>> import simplejson
    >>> simplejson
    <module 'simplejson' from '/home/vagrant/venv/pyneng/lib/python3.11/site-packages/simplejson/__init__.py'>
    >>>

Если выйти из виртуального окружения и попытаться сделать то же
самое, то такого модуля нет:

::

    (pyneng)$ deactivate 

    $ python
    >>> import simplejson
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    ModuleNotFoundError: No module named 'simplejson'
    >>> 