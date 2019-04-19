Виртуальные окружения
=====================

Виртуальные окружения:

-  позволяют изолировать различные проекты друг от друга;
-  пакеты, которые нужны разным проектам, находятся в разных местах –
   если, например, в одном проекте требуется пакет версии 1.0, а в
   другом проекте требуется тот же пакет, но версии 3.1, то они не будут
   мешать друг другу;
-  пакеты, которые установлены в виртуальных окружениях, не перебивают
   глобальные пакеты.

virtualenvwrapper
^^^^^^^^^^^^^^^^^

Виртуальные окружения создаются с помощью virtualenvwrapper.

Установка virtualenvwrapper с помощью pip:

.. code:: shellsession

    $ sudo pip3.6 install virtualenvwrapper

После установки, в файле .bashrc, находящимся в домашней папке текущего
пользователя, нужно добавить несколько строк:

.. code:: shell

    export VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3.6
    export WORKON_HOME=~/venv
    . /usr/local/bin/virtualenvwrapper.sh

Если Вы используете командный интерпретатор, отличный от bash,
посмотрите, поддерживается ли он в
`документации <http://virtualenvwrapper.readthedocs.io/en/latest/install.html>`__
virtualenvwrapper. Переменная окружения VIRTUALENVWRAPPER\_PYTHON
указывает на бинарный файл командной строки Python, WORKON\_HOME – на
расположение виртуальных окружений. Третья строка указывает, где
находится скрипт, установленный с пакетом virtualenvwrapper. Для того,
чтобы скрипт virtualenvwrapper.sh выполнился и можно было работать с
виртуальными окружениями, надо перезапустить bash.

Перезапуск командного интерпретатора:

.. code:: shellsession

    $ exec bash

Такой вариант может быть не всегда правильным. Подробнее на `Stack
Overflow <http://stackoverflow.com/questions/2518127/how-do-i-reload-bashrc-without-logging-out-and-back-in>`__.

Работа с виртуальными окружениями
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Создание нового виртуального окружения, в котором Python 3.6
используется по умолчанию:

.. code:: shellsession

    $ mkvirtualenv --python=/usr/local/bin/python3.6 pyneng
    New python executable in PyNEng/bin/python
    Installing distribute........................done.
    Installing pip...............done.
    (pyneng)$ 

В скобках перед стандартным приглашением отображается имя виртуального
окружения. Это означает, что Вы находитесь в нём. В virtualenvwrapper по
Tab работает автодополнение имени виртуального окружения. Это особенно
удобно в тех случаях, когда виртуальных окружений много. Теперь в том
каталоге, который был указан в переменной окружения WORKON\_HOME, создан
каталог pyneng:

.. code:: shellsession

    (pyneng)$ ls -ls venv
    total 52
    ....
    4 -rwxr-xr-x 1 nata nata   99 Sep 30 16:41 preactivate
    4 -rw-r--r-- 1 nata nata   76 Sep 30 16:41 predeactivate
    4 -rwxr-xr-x 1 nata nata   91 Sep 30 16:41 premkproject
    4 -rwxr-xr-x 1 nata nata  130 Sep 30 16:41 premkvirtualenv
    4 -rwxr-xr-x 1 nata nata  111 Sep 30 16:41 prermvirtualenv
    4 drwxr-xr-x 6 nata nata 4096 Sep 30 16:42 pyneng

Выход из виртуального окружения:

.. code:: shellsession

    (pyneng)$ deactivate 
    $ 

Для перехода в созданное виртуальное окружение надо выполнить команду
workon:

.. code:: shellsession

    $ workon pyneng
    (pyneng)$ 

Если необходимо перейти из одного виртуального окружения в другое, то
необязательно делать deactivate, можно перейти сразу через workon:

.. code:: shellsession

    $ workon Test
    (Test)$ workon pyneng
    (pyneng)$ 

Если виртуальное окружение нужно удалить, то надо использовать команду
rmvirtualenv:

.. code:: shellsession

    $ rmvirtualenv Test
    Removing Test...
    $ 

Посмотреть, какие пакеты установлены в виртуальном окружении можно
командой lssitepackages:

.. code:: shellsession

    (pyneng)$ lssitepackages
    ANSI.py                                pexpect-3.3-py2.7.egg-info
    ANSI.pyc                               pickleshare-0.5-py2.7.egg-info
    decorator-4.0.4-py2.7.egg-info         pickleshare.py
    decorator.py                           pickleshare.pyc
    decorator.pyc                          pip-1.1-py2.7.egg
    distribute-0.6.24-py2.7.egg            pxssh.py
    easy-install.pth                       pxssh.pyc
    fdpexpect.py                           requests
    fdpexpect.pyc                          requests-2.7.0-py2.7.egg-info
    FSM.py                                 screen.py
    FSM.pyc                                screen.pyc
    IPython                                setuptools.pth
    ipython-4.0.0-py2.7.egg-info           simplegeneric-0.8.1-py2.7.egg-info
    ipython_genutils                       simplegeneric.py
    ipython_genutils-0.1.0-py2.7.egg-info  simplegeneric.pyc
    path.py                                test_path.py
    path.py-8.1.1-py2.7.egg-info           test_path.pyc
    path.pyc                               traitlets
    pexpect                                traitlets-4.0.0-py2.7.egg-info

Встроенный модуль venv
^^^^^^^^^^^^^^^^^^^^^^

Начиная с версии 3.5, в Python рекомендуется использовать модуль venv
для создания виртуальных окружений:

.. code:: shellsession

    $ python3.6 -m venv new/pyneng

Вместо python3.6 может использоваться python или python3, в зависимости
от того, как установлен Python 3.6. Эта команда создаёт указанный
каталог и все необходимые каталоги внутри него, если они не были
созданы.

Команда создаёт следующую структуру каталогов:

.. code:: shellsession

    $ ls -ls new/pyneng
    total 16
    4 drwxr-xr-x 2 vagrant vagrant 4096 Aug 21 14:50 bin
    4 drwxr-xr-x 2 vagrant vagrant 4096 Aug 21 14:50 include
    4 drwxr-xr-x 3 vagrant vagrant 4096 Aug 21 14:50 lib
    4 -rw-r--r-- 1 vagrant vagrant   75 Aug 21 14:50 pyvenv.cfg

Для перехода в виртуальное окружение надо выполнить команду:

.. code:: shellsession

    $ source new/pyneng/bin/activate

Для выхода из виртуального окружения используется команда deactivate:

.. code:: shellsession

    $ deactivate

Подробнее о модуле venv в
`документации <https://docs.python.org/3/library/venv.html#module-venv>`__.

Установка пакетов
^^^^^^^^^^^^^^^^^

Например, установим в виртуальном окружении пакет simplejson.

.. code:: shellsession

    (pyneng)$ pip install simplejson
    ...
    Successfully installed simplejson
    Cleaning up...

Если перейти в IPython (рассматривается в `главе
3 <../03_start/README.md>`__) и импортировать simplejson, то он доступен
и никаких ошибок нет:

.. code:: shellsession

    (pyneng)$ ipython

    In [1]: import simplejson

    In [2]: simplejson
    simplejson

    In [2]: simplejson.
    simplejson.Decimal             simplejson.decoder
    simplejson.JSONDecodeError     simplejson.dump
    simplejson.JSONDecoder         simplejson.dumps
    simplejson.JSONEncoder         simplejson.encoder
    simplejson.JSONEncoderForHTML  simplejson.load
    simplejson.OrderedDict         simplejson.loads
    simplejson.absolute_import     simplejson.scanner
    simplejson.compat              simplejson.simple_first

Но если выйти из виртуального окружения и попытаться сделать то же
самое, то такого модуля нет:

.. code:: shellsession

    (pyneng)$ deactivate 

    $ ipython

    In [1]: import simplejson
    ------------------------------------------------------------------
    ImportError                               Traceback (most recent call last)
    <ipython-input-1-ac998a77e3e2> in <module>()
    ----> 1 import simplejson

    ImportError: No module named simplejson

