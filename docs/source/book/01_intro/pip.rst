Система управления пакетами pip
-------------------------------

Для установки пакетов Python будет использоваться pip. Это система
управления пакетами, которая используется для установки пакетов из
Python Package Index (PyPI). Скорее всего, если у вас уже установлен
Python, то установлен и pip.

Проверка версии pip:

::

    $ pip --version
    pip 19.1.1 from /home/vagrant/venv/pyneng-py3-7/lib/python3.7/site-packages/pip (python 3.7)


Если команда выдала ошибку, значит, pip не установлен. Установка pip
описана в `документации <https://pip.pypa.io/en/stable/installing/>`__

Установка модулей
^^^^^^^^^^^^^^^^^

Для установки модулей используется команда ``pip install``:

::

    $ pip install tabulate

Удаление пакета выполняется таким образом:

::

    $ pip uninstall tabulate

Кроме того, иногда необходимо обновить пакет:

::

    $ pip install --upgrade tabulate

pip или pip3
^^^^^^^^^^^^

В зависимости от того, как установлен и настроен Python в системе, может
потребоваться использовать pip3 вместо pip. Чтобы проверить, какой
вариант используется, надо выполнить команду ``pip --version``.

Вариант, когда pip соответствует Python 2.7:

::

    $ pip --version
    pip 9.0.1 from /usr/local/lib/python2.7/dist-packages (python 2.7)

Вариант, когда pip3 соответствует Python 3.7:

::

    $ pip3 --version
    pip 19.1.1 from /home/vagrant/venv/pyneng-py3-7/lib/python3.7/site-packages/pip (python 3.7)


Если в системе используется pip3, то каждый раз, когда в книге
устанавливается модуль Python, нужно будет заменить pip на pip3.

Также можно использовать альтернативный вариант вызова pip:

::

    $ python3.7 -m pip install tabulate

Таким образом, всегда понятно для какой именно версии Python
устанавливается пакет.
