Модуль logging
--------------

Модуль logging - это модуль из стандартной библиотеки Python, который позволяет
настраивать логирование из скрипта.
У модуля logging очень много возможностей и огромное количество вариантов настройки.
В этом разделе рассматривается только базовый вариант настройки.

Самый простой вариант настройки логирования в скрипте, использовать logging.basicConfig:

.. code:: python

    import logging


    logging.basicConfig(
        format='%(threadName)s %(name)s %(levelname)s: %(message)s',
        level=logging.INFO)

В таком варианте настройки:

* все сообщения будут выводиться на стандартный поток вывода, 
* будут выводиться сообщения уровня INFO и выше, 
* в каждом сообщении будет информация о потоке, имя логера, уровень сообщения и само сообщение.

Теперь, чтобы вывести log-сообщение в этом скрипте, надо написать так ``logging.info("тест")``.

Пример скрипта с настройкой логирования: (файл logging_basics.py)

.. literalinclude:: /pyneng-examples-exercises/examples/20_concurrent_connections/logging_basics.py
  :language: python
  :linenos:

При выполнении скрипта, вывод будет таким:

::

    $ python logging_basics.py
    MainThread root INFO: ===> 12:26:12.767168 Connection: 192.168.100.1
    MainThread root INFO: <=== 12:26:18.307017 Received:   192.168.100.1
    *12:26:18.137 UTC Wed Jun 5 2019
    MainThread root INFO: ===> 12:26:18.413913 Connection: 192.168.100.2
    MainThread root INFO: <=== 12:26:23.991715 Received:   192.168.100.2
    *12:26:23.819 UTC Wed Jun 5 2019
    MainThread root INFO: ===> 12:26:24.095452 Connection: 192.168.100.3
    MainThread root INFO: <=== 12:26:29.478553 Received:   192.168.100.3
    *12:26:29.308 UTC Wed Jun 5 2019

.. note::

    В модуле logging еще очень много возможностей. В этом разделе используется только
    базовый вариант настройки.
    Узнать больше о возможностях модуля можно в `Logging HOWTO <https://docs.python.org/3/howto/logging.html#logging-basic-tutorial>`__
