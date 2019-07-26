Использование ProcessPoolExecutor
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Интерфейс модуля concurrent.futures очень удобен тем, что переход с потоков
на процессы делается заменой ThreadPoolExecutor на ProcessPoolExecutor, 
поэтому все примеры ниже полностью аналогичны примерам с потоками.

Метод map
^^^^^^^^^

Для того чтобы использовать процессы вместо потоков,
достаточно сменить ThreadPoolExecutor на ProcessPoolExecutor:


.. literalinclude:: /pyneng-examples-exercises/examples/20_concurrent_connections/netmiko_processes_map.py
  :language: python
  :linenos:

Результат выполнения:

::

    $ python netmiko_processes_map.py
    MainThread root INFO: ===> 08:35:50.931629 Connection: 192.168.100.2
    MainThread root INFO: ===> 08:35:50.931295 Connection: 192.168.100.1
    MainThread root INFO: <=== 08:35:56.353774 Received:   192.168.100.2
    MainThread root INFO: ===> 08:35:56.469854 Connection: 192.168.100.3
    MainThread root INFO: <=== 08:36:01.410230 Received:   192.168.100.1
    MainThread root INFO: <=== 08:36:02.067678 Received:   192.168.100.3
    {'192.168.100.1': '*08:36:01.242 UTC Fri Jul 26 2019',
     '192.168.100.2': '*08:35:56.185 UTC Fri Jul 26 2019',
     '192.168.100.3': '*08:36:01.900 UTC Fri Jul 26 2019'}


Метод submit
^^^^^^^^^^^^

Файл netmiko_processes_submit_exception.py:

.. literalinclude:: /pyneng-examples-exercises/examples/20_concurrent_connections/netmiko_processes_submit_exception.py
  :language: python
  :linenos:

Результат выполнения:

::

    $ python netmiko_processes_submit_exception.py
    MainThread root INFO: ===> 08:38:08.780267 Connection: 192.168.100.1
    MainThread root INFO: ===> 08:38:08.781355 Connection: 192.168.100.2
    MainThread root INFO: <=== 08:38:14.420339 Received: 192.168.100.2
    MainThread root INFO: ===> 08:38:14.529405 Connection: 192.168.100.3
    MainThread root INFO: <=== 08:38:19.224554 Received: 192.168.100.1
    MainThread root INFO: <=== 08:38:20.162920 Received: 192.168.100.3
    {'192.168.100.1': '*08:38:19.058 UTC Fri Jul 26 2019',
     '192.168.100.2': '*08:38:14.250 UTC Fri Jul 26 2019',
     '192.168.100.3': '*08:38:19.995 UTC Fri Jul 26 2019'}

