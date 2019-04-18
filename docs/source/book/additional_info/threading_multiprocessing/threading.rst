Модуль threading
----------------

Модуль threading может быть полезен для таких задач: \* фоновое
выполнение каких-то задач: \* например, отправка почты во время ожидания
ответа от пользователя \* параллельное выполнение задач, связанных со
вводом/выводом \* ожидание ввода от пользователя \* чтение/запись файлов
\* задачи, где присутствуют паузы: \* например, паузы с помощью sleep

Однако следует учитывать, что в ситуациях, когда требуется повышение
производительности за счет использования нескольких процессоров или
ядер, нужно использовать модуль multiprocessing, а не модуль threading.

Рассмотрим пример использования модуля threading вместе с последним
примером с netmiko.

Так как для работы с threading удобнее использовать функции, код
изменен: \* код подключения по SSH перенесён в функцию \* параметры
устройств перенесены в отдельный файл в формате YAML

Файл netmiko\_function.py:

.. code:: python

    import sys
    import yaml
    from netmiko import ConnectHandler

    #COMMAND = sys.argv[1]
    devices = yaml.load(open('devices.yaml'))


    def connect_ssh(device_dict, commands):

        print('Connection to device {}'.format( device_dict['ip'] ))

        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()

            result = ssh.send_config_set(commands)
            print(result)


    commands_to_send = ['logg 10.1.12.3', 'ip access-li ext TESST2', 'permit ip any any']

    for router in devices['routers']:
        connect_ssh(router, commands_to_send)

Файл devices.yaml с параметрами подключения к устройствам:

.. code:: yaml

    routers:
    - device_type: cisco_ios
      ip: 192.168.100.1
      username: cisco
      password: cisco
      secret: cisco
    - device_type: cisco_ios
      ip: 192.168.100.2
      username: cisco
      password: cisco
      secret: cisco
    - device_type: cisco_ios
      ip: 192.168.100.3
      username: cisco
      password: cisco
      secret: cisco

Время выполнения скрипта (вывод скрипта удален):

::

    $ time python netmiko_function.py "sh ip int br"
    ...
    real    0m6.189s
    user    0m0.336s
    sys     0m0.080s

Пример использования модуля threading для подключения по SSH с помощью
netmiko (файл netmiko\_threading.py):

.. code:: python

    import sys
    import yaml
    import threading

    from netmiko import ConnectHandler


    COMMAND = sys.argv[1]
    devices = yaml.load(open('devices.yaml'))


    def connect_ssh(device_dict, command):
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)

            print('Connection to device {}'.format( device_dict['ip'] ))
            print(result)


    def conn_threads(function, devices, command):
        threads = []
        for device in devices:
            th = threading.Thread(target = function, args = (device, command))
            th.start()
            threads.append(th)

        for th in threads:
            th.join()


    conn_threads(connect_ssh, devices['routers'], COMMAND)

Время выполнения кода:

::

    $ time python netmiko_function_threading.py "sh ip int br"

    ...
    real    0m2.229s
    user    0m0.408s
    sys     0m0.068s

Время почти в три раза меньше. Но надо учесть, что такая ситуация не
будет повторяться при большом количестве подключений.

Комментарии к функции conn\_threads: \* ``threading.Thread`` - класс,
который создает поток \* ему передается функция, которую надо выполнить,
и её аргументы \* ``th.start()`` - запуск потока \*
``threads.append(th)`` - поток добавляется в список \* ``th.join()`` -
метод ожидает завершения работы потока \* метод join выполняется для
каждого потока в списке. Таким образом, основная программа завершится,
только когда завершат работу все потоки \* по умолчанию ``join`` ждет
завершения работы потока бесконечно. Но можно ограничить время ожидания,
передав ``join`` время в секундах. В таком случае ``join`` завершится
после указанного количества секунд.

Получение данных из потоков
~~~~~~~~~~~~~~~~~~~~~~~~~~~

В предыдущем примере данные выводились на стандартный поток вывода. Для
полноценной работы с потоками необходимо также научиться получать данные
из потоков. Чаще всего для этого используется очередь.

В Python есть модуль queue, который позволяет создавать разные типы
очередей.

    Очередь - это структура данных, которая используется и в работе с
    сетевым оборудованием. Объект queue.Queue() - это FIFO очередь.

Очередь передается как аргумент в функцию connect\_ssh, которая
подключается к устройству по SSH. Результат выполнения команды
добавляется в очередь.

Пример использования потоков с получением данных (файл
netmiko\_threading\_data.py):

.. code:: python

    # -*- coding: utf-8 -*-
    import sys
    import yaml
    import threading
    from queue import Queue
    from pprint import pprint
    from netmiko import ConnectHandler


    COMMAND = sys.argv[1]
    devices = yaml.load(open('devices.yaml'))


    def connect_ssh(device_dict, command, queue):
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)
            print('Connection to device {}'.format(device_dict['ip']))

            #Добавляем словарь в очередь
            queue.put({device_dict['ip']: result})


    def conn_threads(function, devices, command):
        threads = []
        q = Queue()

        for device in devices:
            # Передаем очередь как аргумент, функции
            th = threading.Thread(target=function, args=(device, command, q))
            th.start()
            threads.append(th)

        for th in threads:
            th.join()

        results = []
        # Берем результаты из очереди и добавляем их в список results
        for t in threads:
            results.append(q.get())

        return results

    pprint(conn_threads(connect_ssh, devices['routers'], COMMAND))

Обратите внимание, что в функции connect\_ssh добавился аргумент queue.

Очередь вполне можно воспринимать как список: \* метод ``queue.put()``
равнозначен ``list.append()`` \* метод ``queue.get()`` равнозначен
``list.pop(0)``

Для работы с потоками и модулем threading лучше использовать очередь.

Очередь лучше тем, что она поддерживает только две операции по изменению
содержимого: \* добавить элемент - ``queue.put()`` \* взять элемент -
``queue.get()``

А список, кроме этих операций, поддерживает изменение элементов,
переприсваивание значений. И при работе с потоками, используя эти
операции, можно получить совсем не тот результат, который ожидался.

Но пример со списком, скорее всего, будет проще понять. И при
использовании методов append и pop никаких проблем не будет.

Ниже аналогичный код, но с использованием обычного списка вместо очереди
(файл netmiko\_threading\_data\_list.py):

.. code:: python

    # -*- coding: utf-8 -*-
    import sys
    import yaml
    import threading
    from pprint import pprint

    from netmiko import ConnectHandler


    COMMAND = sys.argv[1]
    devices = yaml.load(open('devices.yaml'))


    def connect_ssh(device_dict, command, queue):
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)
            print('Connection to device {}'.format( device_dict['ip'] ))

            #Добавляем словарь в список
            queue.append({ device_dict['ip']: result })


    def conn_threads(function, devices, command):
        threads = []
        q = []

        for device in devices:
            # Передаем список как аргумент, функции
            th = threading.Thread(target = function, args = (device, command, q))
            th.start()
            threads.append(th)

        for th in threads:
            th.join()

        return q

    result = conn_threads(connect_ssh, devices['routers'], COMMAND)
    pprint(result)

