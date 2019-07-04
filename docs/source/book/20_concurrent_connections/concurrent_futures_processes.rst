Использование ProcessPoolExecutor
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Метод map
^^^^^^^^^
Для того чтобы предыдущий пример использовал процессы вместо потоков,
достаточно сменить ThreadPoolExecutor на ProcessPoolExecutor:

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

Метод submit
^^^^^^^^^^^^

Так как все работает аналогичным образом и для процессов, тут приведет
последний вариант (файл netmiko\_processes\_submit\_exception.py):

.. code:: python

    from concurrent.futures import ProcessPoolExecutor, as_completed
    from pprint import pprint
    from datetime import datetime
    import time

    import yaml
    from netmiko import ConnectHandler
    from netmiko.ssh_exception import NetMikoAuthenticationException


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


    def processes_conn(function, devices, limit=2, command=''):
        all_results = {}
        with ProcessPoolExecutor(max_workers=limit) as executor:
            future_ssh = [executor.submit(function, device, command)
                          for device in devices]
            for f in as_completed(future_ssh):
                try:
                    result = f.result()
                except NetMikoAuthenticationException as e:
                    print(e)
                else:
                    all_results.update(result)
        return all_results


    if __name__ == '__main__':
        devices = yaml.load(open('devices.yaml'))
        all_done = processes_conn(connect_ssh,
                                  devices['routers'],
                                  command='sh clock')
        pprint(all_done)

Результат выполнения:

::

    $ python netmiko_processes_submit_exception.py
    ===> 06:40:43.828249 Connection to device: 192.168.100.1
    ===> 06:40:43.828664 Connection to device: 192.168.100.2
    Authentication failure: unable to connect cisco_ios 192.168.100.2:22
    Authentication failed.
    ===> 06:40:46.292613 Connection to device: 192.168.100.3
    <=== 06:40:51.890816 Received result from device: 192.168.100.3
    <=== 06:40:59.231330 Received result from device: 192.168.100.1
    {'192.168.100.1': '*06:40:59.056 UTC Mon Aug 28 2017',
     '192.168.100.3': '*06:40:51.719 UTC Mon Aug 28 2017'}

