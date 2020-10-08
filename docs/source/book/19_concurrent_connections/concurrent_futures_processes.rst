Использование ProcessPoolExecutor
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Интерфейс модуля concurrent.futures очень удобен тем, что переход с потоков
на процессы делается заменой ThreadPoolExecutor на ProcessPoolExecutor, 
поэтому все примеры ниже полностью аналогичны примерам с потоками.

Метод map
^^^^^^^^^

Для того чтобы использовать процессы вместо потоков,
достаточно сменить ThreadPoolExecutor на ProcessPoolExecutor:


.. code:: python

    from concurrent.futures import ProcessPoolExecutor
    from pprint import pprint
    from datetime import datetime
    import time
    from itertools import repeat
    import logging

    import yaml
    from netmiko import ConnectHandler, NetMikoAuthenticationException


    logging.getLogger('paramiko').setLevel(logging.WARNING)

    logging.basicConfig(
        format = '%(threadName)s %(name)s %(levelname)s: %(message)s',
        level=logging.INFO)


    def send_show(device_dict, command):
        start_msg = '===> {} Connection: {}'
        received_msg = '<=== {} Received:   {}'
        ip = device_dict['ip']
        logging.info(start_msg.format(datetime.now().time(), ip))
        if ip == '192.168.100.1': time.sleep(5)

        try:
            with ConnectHandler(**device_dict) as ssh:
                ssh.enable()
                result = ssh.send_command(command)
                logging.info(received_msg.format(datetime.now().time(), ip))
            return result
        except NetMikoAuthenticationException as err:
            logging.warning(err)


    def send_command_to_devices(devices, command):
        data = {}
        with ProcessPoolExecutor(max_workers=2) as executor:
            result = executor.map(send_show, devices, repeat(command))
            for device, output in zip(devices, result):
                data[device['ip']] = output
        return data


    if __name__ == '__main__':
        with open('devices.yaml') as f:
            devices = yaml.safe_load(f)
        pprint(send_command_to_devices(devices, 'sh clock'))


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

.. code:: python

    from concurrent.futures import ProcessPoolExecutor, as_completed
    from pprint import pprint
    from datetime import datetime
    import time
    from itertools import repeat
    import logging

    import yaml
    from netmiko import ConnectHandler
    from netmiko.ssh_exception import NetMikoAuthenticationException

    logging.getLogger("paramiko").setLevel(logging.WARNING)

    logging.basicConfig(
        format = '%(threadName)s %(name)s %(levelname)s: %(message)s',
        level=logging.INFO)

    start_msg = '===> {} Connection: {}'
    received_msg = '<=== {} Received: {}'


    def send_show(device_dict, command):
        ip = device_dict['ip']
        logging.info(start_msg.format(datetime.now().time(), ip))
        if ip == '192.168.100.1': time.sleep(5)
        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)
            logging.info(received_msg.format(datetime.now().time(), ip))
        return {ip: result}


    def send_command_to_devices(devices, command):
        data = {}
        with ProcessPoolExecutor(max_workers=2) as executor:
            future_ssh = [
                executor.submit(send_show, device, command) for device in devices
            ]
            for f in as_completed(future_ssh):
                try:
                    result = f.result()
                except NetMikoAuthenticationException as e:
                    print(e)
                else:
                    data.update(result)
        return data


    if __name__ == '__main__':
        with open('devices.yaml') as f:
            devices = yaml.safe_load(f)
        pprint(send_command_to_devices(devices, 'sh clock'))


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

