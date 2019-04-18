Метод map
---------

Метод map - это самый простой вариант работы с concurrent.futures.

Пример использования функции map с ThreadPoolExecutor (файл
netmiko\_threads\_map\_ver1.py):

.. code:: python

    from concurrent.futures import ThreadPoolExecutor
    from pprint import pprint

    import yaml
    from netmiko import ConnectHandler


    def connect_ssh(device_dict, command='sh clock'):
        print('Connection to device: {}'.format(device_dict['ip']))
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)
        return {device_dict['ip']: result}


    def threads_conn(function, devices, limit=2):
        with ThreadPoolExecutor(max_workers=limit) as executor:
            f_result = executor.map(function, devices)
        return list(f_result)


    if __name__ == '__main__':
        devices = yaml.load(open('devices.yaml'))
        all_done = threads_conn(connect_ssh, devices['routers'])
        pprint(all_done)

Это первый ограниченный пример, так как сейчас в функции connect\_ssh
команда задана вручную, а не передается как аргумент. В следующей версии
это будет исправлено.

Сейчас нас интересует функция threads\_conn:

.. code:: python

    def threads_conn(function, devices, limit=2):
        with ThreadPoolExecutor(max_workers=limit) as executor:
            f_result = executor.map(function, devices)
        return list(f_result)

-  ``with ThreadPoolExecutor(max_workers=limit) as executor:`` - класс
   ThreadPoolExecutor инициируется в блоке with с указанием количества
   потоков
-  ``f_result = executor.map(function, devices)`` - метод map похож на
   функцию map, но тут функция function вызывается в разных потоках. При
   этом в разных потоках функция будет вызываться с разными аргументами
   - элементами итерируемого объекта devices.
-  метод map возвращает генератор. В этом генераторе содержатся
   результаты выполнения функций

Обратите внимание, что функция занимает всего 4 строки, и для получения
данных не надо создавать очередь и передавать ее в функцию connect\_ssh.

Результат выполнения:

::

    $ python netmiko_threads_map_ver1.py
    Connection to device: 192.168.100.1
    Connection to device: 192.168.100.2
    Connection to device: 192.168.100.3
    [{'192.168.100.1': '*04:43:01.629 UTC Mon Aug 28 2017'},
     {'192.168.100.2': '*04:43:01.648 UTC Mon Aug 28 2017'},
     {'192.168.100.3': '*04:43:07.291 UTC Mon Aug 28 2017'}]

Важная особенность метода map - он возвращает результаты в том же
порядке, в котором они указаны в итерируемом объекте.

Для демонстрации этой особенности в функции connect\_ssh добавлены
сообщения с выводом информации о том, когда функция начала работать и
когда закончила. А также для маршрутизатора с IP 192.168.100.1 добавлен
sleep, чтобы задержать выполнение функции (файл
netmiko\_threads\_map\_ver2.py):

.. code:: python

    from concurrent.futures import ThreadPoolExecutor
    from pprint import pprint
    from datetime import datetime
    import time

    import yaml
    from netmiko import ConnectHandler


    start_msg = '===> {} Connection to device: {}'
    received_msg = '<=== {} Received result from device: {}'


    def connect_ssh(device_dict, command='sh clock'):
        print(start_msg.format(datetime.now().time(), device_dict['ip']))
        if device_dict['ip'] == '192.168.100.1':
            time.sleep(10)
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)
            print(received_msg.format(datetime.now().time(), device_dict['ip']))
        return {device_dict['ip']: result}


    def threads_conn(function, devices, limit=2):
        with ThreadPoolExecutor(max_workers=limit) as executor:
            f_result = executor.map(function, devices)
        return list(f_result)


    if __name__ == '__main__':
        devices = yaml.load(open('devices.yaml'))
        all_done = threads_conn(connect_ssh, devices['routers'])
        pprint(all_done)

Результат выполнения:

::

    $ python netmiko_threads_map_ver2.py
    ===> 04:50:50.175076 Connection to device: 192.168.100.1
    ===> 04:50:50.175553 Connection to device: 192.168.100.2
    <=== 04:50:55.582707 Received result from device: 192.168.100.2
    ===> 04:50:55.689248 Connection to device: 192.168.100.3
    <=== 04:51:01.135640 Received result from device: 192.168.100.3
    <=== 04:51:05.568037 Received result from device: 192.168.100.1
    [{'192.168.100.1': '*04:51:05.395 UTC Mon Aug 28 2017'},
     {'192.168.100.2': '*04:50:55.411 UTC Mon Aug 28 2017'},
     {'192.168.100.3': '*04:51:00.964 UTC Mon Aug 28 2017'}]

Обратите внимание на фактический порядок выполнения задач:
192.168.100.2, 192.168.100.3, 192.168.100.1. Но в итоговом списке все
равно соблюдается порядок на основе списка devices['routers'].

Еще один момент, который тут хорошо заметен, это то, что как только одна
задача выполнилась, сразу берется следующая. То есть, ограничение в два
потока влияет на количество потоков, которые выполняются одновременно.

Теперь осталось изменить функцию таким образом, чтобы ей можно было
передавать команду как аргумент.

Для этого мы воспользуемся функцией repeat из модуля itertools (файл
netmiko\_threads\_map\_final.py):

.. code:: python

    from concurrent.futures import ThreadPoolExecutor
    from pprint import pprint
    from datetime import datetime
    import time
    from itertools import repeat

    import yaml
    from netmiko import ConnectHandler


    start_msg = '===> {} Connection to device: {}'
    received_msg = '<=== {} Received result from device: {}'


    def connect_ssh(device_dict, command):
        print(start_msg.format(datetime.now().time(), device_dict['ip']))
        if device_dict['ip'] == '192.168.100.1':
            time.sleep(10)
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)
            print(received_msg.format(datetime.now().time(), device_dict['ip']))
        return {device_dict['ip']: result}



    def threads_conn(function, devices, limit=2, command=''):
        with ThreadPoolExecutor(max_workers=limit) as executor:
            f_result = executor.map(function, devices, repeat(command))
        return list(f_result)


    if __name__ == '__main__':
        devices = yaml.load(open('devices.yaml'))
        all_done = threads_conn(connect_ssh,
                                devices['routers'],
                                command='sh clock')
        pprint(all_done)

Функция repeat тут нужна для того, чтобы команда передавалась при каждом
вызове функции connect\_ssh.

Результат выполнения:

::

    $ python netmiko_threads_map_final.py
    ===> 05:01:08.314962 Connection to device: 192.168.100.1
    ===> 05:01:08.315114 Connection to device: 192.168.100.2
    <=== 05:01:13.693083 Received result from device: 192.168.100.2
    ===> 05:01:13.799002 Connection to device: 192.168.100.3
    <=== 05:01:19.363250 Received result from device: 192.168.100.3
    <=== 05:01:23.685859 Received result from device: 192.168.100.1
    [{'192.168.100.1': '*05:01:23.513 UTC Mon Aug 28 2017'},
     {'192.168.100.2': '*05:01:13.522 UTC Mon Aug 28 2017'},
     {'192.168.100.3': '*05:01:19.189 UTC Mon Aug 28 2017'}]

Использование ProcessPoolExecutor с map
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Для того чтобы предыдущий пример использовал процессы вместо потоков,
достаточно сменить ThreadPoolExecutor на ProcessPoolExecutor ():

.. code:: python

    from concurrent.futures import ProcessPoolExecutor
    from pprint import pprint
    from datetime import datetime
    import time
    from itertools import repeat

    import yaml
    from netmiko import ConnectHandler


    start_msg = '===> {} Connection to device: {}'
    received_msg = '<=== {} Received result from device: {}'


    def connect_ssh(device_dict, command):
        print(start_msg.format(datetime.now().time(), device_dict['ip']))
        if device_dict['ip'] == '192.168.100.1':
            time.sleep(10)
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)
            print(received_msg.format(datetime.now().time(), device_dict['ip']))
        return {device_dict['ip']: result}



    def threads_conn(function, devices, limit=2, command=''):
        with ProcessPoolExecutor(max_workers=limit) as executor:
            f_result = executor.map(function, devices, repeat(command))
        return list(f_result)


    if __name__ == '__main__':
        devices = yaml.load(open('devices.yaml'))
        all_done = threads_conn(connect_ssh,
                                devices['routers'],
                                command='sh clock')
        pprint(all_done)

Результат выполнения:

::

    $ python netmiko_processes_map_final.py
    ===> 05:26:42.974505 Connection to device: 192.168.100.1
    ===> 05:26:42.975733 Connection to device: 192.168.100.2
    <=== 05:26:48.389420 Received result from device: 192.168.100.2
    ===> 05:26:48.495598 Connection to device: 192.168.100.3
    <=== 05:26:54.104585 Received result from device: 192.168.100.3
    <=== 05:26:58.367981 Received result from device: 192.168.100.1
    [{'192.168.100.1': '*05:26:58.195 UTC Mon Aug 28 2017'},
     {'192.168.100.2': '*05:26:48.218 UTC Mon Aug 28 2017'},
     {'192.168.100.3': '*05:26:53.932 UTC Mon Aug 28 2017'}]

