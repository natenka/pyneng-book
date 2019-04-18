Модуль multiprocessing
----------------------

Модуль multiprocessing использует интерфейс, подобный модулю threading.
Поэтому перенести код с использования потоков на использование процессов
обычно достаточно легко.

Каждому процессу выделяются свои ресурсы. Кроме того, у каждого процесса
свой GIL, а значит, нет тех проблем, которые были с потоками, и код
может выполняться параллельно и задействовать ядра/процессоры
компьютера.

Пример использования модуля multiprocessing (файл
netmiko\_multiprocessing.py):

.. code:: python

    import multiprocessing
    import sys
    import yaml
    from pprint import pprint

    from netmiko import ConnectHandler


    COMMAND = sys.argv[1]
    devices = yaml.load(open('devices.yaml'))


    def connect_ssh(device_dict, command, queue):
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)

            print('Connection to device {}'.format(device_dict['ip']))
            queue.put({device_dict['ip']: result})


    def conn_processes(function, devices, command):
        processes = []
        queue = multiprocessing.Queue()

        for device in devices:
            p = multiprocessing.Process(target=function,
                                        args=(device, command, queue))
            p.start()
            processes.append(p)

        for p in processes:
            p.join()

        results = []
        for p in processes:
            results.append(queue.get())

        return results


    pprint(conn_processes(connect_ssh, devices['routers'], COMMAND))

Обратите внимание, что этот пример аналогичен последнему примеру,
который использовался с модулем threading. Единственное отличие в том,
что в модуле multiprocessing есть своя реализация очереди, поэтому нет
необходимости использовать модуль Queue.

Если проверить время исполнения этого скрипта, аналогичного для модуля
threading и последовательного подключения, то получаем такую картину:

::

    последовательное: 5.833s
    threading:        2.225s
    multiprocessing:  2.365s

Время выполнения для модуля multiprocessing немного больше. Но это
связано с тем, что на создание процессов уходит больше времени, чем на
создание потоков. Если бы скрипт был сложнее и выполнялось больше задач,
или было бы больше подключений, тогда бы multiprocessing начал бы
существенно выигрывать у модуля threading.
