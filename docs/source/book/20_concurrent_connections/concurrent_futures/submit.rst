Метод submit и работа с futures
-------------------------------

При использовании метода map объект future использовался внутри, но в
итоге мы получали уже готовый результат функции.

В модуле concurrent.futures есть метод submit, который позволяет
запускать future, и функция as\_completed, которая ожидает как аргумент
итерируемый объект с futures и возвращает future по мере завершения. В
этом случае порядок не будет соблюдаться, как с map.

Теперь функция threads\_conn выглядит немного по-другому (файл
netmiko\_threads\_submit.py):

.. code:: python

    from concurrent.futures import ThreadPoolExecutor, as_completed
    from pprint import pprint
    from datetime import datetime
    import time

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
        all_results = []
        with ThreadPoolExecutor(max_workers=limit) as executor:
            future_ssh = [executor.submit(function, device, command)
                          for device in devices]
            for f in as_completed(future_ssh):
                all_results.append(f.result())
        return all_results


    if __name__ == '__main__':
        devices = yaml.load(open('devices.yaml'))
        all_done = threads_conn(connect_ssh,
                                devices['routers'],
                                command='sh clock')
        pprint(all_done)

Остальной код не изменился, поэтому разобраться надо только с функцией
threads\_conn:

.. code:: python

    def threads_conn(function, devices, limit=2, command=''):
        all_results = []
        with ThreadPoolExecutor(max_workers=limit) as executor:
            future_ssh = [executor.submit(function, device, command)
                          for device in devices]
            for f in as_completed(future_ssh):
                all_results.append(f.result())
        return all_results

Теперь в блоке with два цикла: \* ``future_ssh`` - это список объектов
future, который создается с помощью list comprehensions \* для создания
future используется функция submit \* ей как аргументы передаются: имя
функции, которую надо выполнить, и ее аргументы \* следующий цикл
проходится по списку future с помощью функции as\_completed. Эта функция
возвращает future только когда они завершили работу или были отменены.
При этом future возвращаются по мере завершения работы

Результат выполнения:

::

    $ python netmiko_threads_submit.py
    ===> 06:02:14.582011 Connection to device: 192.168.100.1
    ===> 06:02:14.582155 Connection to device: 192.168.100.2
    <=== 06:02:20.155865 Received result from device: 192.168.100.2
    ===> 06:02:20.262584 Connection to device: 192.168.100.3
    <=== 06:02:25.864270 Received result from device: 192.168.100.3
    <=== 06:02:29.962225 Received result from device: 192.168.100.1
    [{'192.168.100.2': '*06:02:19.983 UTC Mon Aug 28 2017'},
     {'192.168.100.3': '*06:02:25.691 UTC Mon Aug 28 2017'},
     {'192.168.100.1': '*06:02:29.789 UTC Mon Aug 28 2017'}]

Обратите внимание, что порядок не сохраняется и зависит от того, какие
функции раньше завершили работу.

Future
~~~~~~

Чтобы посмотреть на future, в скрипт добавлены несколько строк с выводом
информации (netmiko\_threads\_submit\_verbose.py):

.. code:: python

    from concurrent.futures import ThreadPoolExecutor, as_completed
    from pprint import pprint
    from datetime import datetime
    import time

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
        all_results = {}
        with ThreadPoolExecutor(max_workers=limit) as executor:
            future_ssh = []
            for device in devices:
                future = executor.submit(function, device, command)
                future_ssh.append(future)
                print('Future: {} for device {}'.format(future, device['ip']))
            for f in as_completed(future_ssh):
                result = f.result()
                print('Future done {}'.format(f))
                all_results.update(result)
        return all_results


    if __name__ == '__main__':
        devices = yaml.load(open('devices.yaml'))
        all_done = threads_conn(connect_ssh,
                                devices['routers'],
                                command='sh clock')
        pprint(all_done)

    Так как в прошлом варианте мы уже проверили, что результат
    возвращается в порядке выполнения, тут функция threads\_conn
    возвращает словарь, а не список.

Результат выполнения:

::

    $ python netmiko_threads_submit_verbose.py
    ===> 06:16:56.059256 Connection to device: 192.168.100.1
    Future: <Future at 0xb68427cc state=running> for device 192.168.100.1
    ===> 06:16:56.059434 Connection to device: 192.168.100.2
    Future: <Future at 0xb68483ac state=running> for device 192.168.100.2
    Future: <Future at 0xb6848b4c state=pending> for device 192.168.100.3
    <=== 06:17:01.482761 Received result from device: 192.168.100.2
    ===> 06:17:01.589605 Connection to device: 192.168.100.3
    Future done <Future at 0xb68483ac state=finished returned dict>
    <=== 06:17:07.226815 Received result from device: 192.168.100.3
    Future done <Future at 0xb6848b4c state=finished returned dict>
    <=== 06:17:11.444831 Received result from device: 192.168.100.1
    Future done <Future at 0xb68427cc state=finished returned dict>
    {'192.168.100.1': '*06:17:11.273 UTC Mon Aug 28 2017',
     '192.168.100.2': '*06:17:01.310 UTC Mon Aug 28 2017',
     '192.168.100.3': '*06:17:07.055 UTC Mon Aug 28 2017'}

Так как по умолчанию используется ограничение в два потока, только два
из трех future показывают статус running. Третий находится в состоянии
pending и ждет, пока до него дойдет очередь.

Обработка исключений
~~~~~~~~~~~~~~~~~~~~

Если при выполнении функции возникло исключение, оно будет сгенерировано
при получении результата

Например, в файле devices.yaml пароль для устройства 192.168.100.2
изменен на неправильный:

::

    $ python netmiko_threads_submit.py
    ===> 06:29:40.871851 Connection to device: 192.168.100.1
    ===> 06:29:40.872888 Connection to device: 192.168.100.2
    ===> 06:29:43.571296 Connection to device: 192.168.100.3
    <=== 06:29:48.921702 Received result from device: 192.168.100.3
    <=== 06:29:56.269284 Received result from device: 192.168.100.1
    Traceback (most recent call last):
      File "/home/vagrant/venv/py3_convert/lib/python3.6/site-packages/netmiko/base_connection.py", line 491, in establish_connection
        self.remote_conn_pre.connect(**ssh_connect_params)
      File "/home/vagrant/venv/py3_convert/lib/python3.6/site-packages/paramiko/client.py", line 394, in connect
        look_for_keys, gss_auth, gss_kex, gss_deleg_creds, gss_host)
      File "/home/vagrant/venv/py3_convert/lib/python3.6/site-packages/paramiko/client.py", line 649, in _auth
        raise saved_exception
      File "/home/vagrant/venv/py3_convert/lib/python3.6/site-packages/paramiko/client.py", line 636, in _auth
        self._transport.auth_password(username, password)
      File "/home/vagrant/venv/py3_convert/lib/python3.6/site-packages/paramiko/transport.py", line 1329, in auth_password
        return self.auth_handler.wait_for_response(my_event)
      File "/home/vagrant/venv/py3_convert/lib/python3.6/site-packages/paramiko/auth_handler.py", line 217, in wait_for_response
        raise e
    paramiko.ssh_exception.AuthenticationException: Authentication failed.

    During handling of the above exception, another exception occurred:

    Traceback (most recent call last):
      File "netmiko_threads_submit.py", line 40, in <module>
        command='sh clock')
      File "netmiko_threads_submit.py", line 32, in threads_conn
        all_results.append(f.result())
      File "/usr/local/lib/python3.6/concurrent/futures/_base.py", line 398, in result
        return self.__get_result()
      File "/usr/local/lib/python3.6/concurrent/futures/_base.py", line 357, in __get_result
        raise self._exception
      File "/usr/local/lib/python3.6/concurrent/futures/thread.py", line 55, in run
        result = self.fn(*self.args, **self.kwargs)
      File "netmiko_threads_submit.py", line 19, in connect_ssh
        with ConnectHandler(**device_dict) as ssh:
      File "/home/vagrant/venv/py3_convert/lib/python3.6/site-packages/netmiko/ssh_dispatcher.py", line 122, in ConnectHandler
        return ConnectionClass(*args, **kwargs)
      File "/home/vagrant/venv/py3_convert/lib/python3.6/site-packages/netmiko/base_connection.py", line 145, in __init__
        self.establish_connection()
      File "/home/vagrant/venv/py3_convert/lib/python3.6/site-packages/netmiko/base_connection.py", line 500, in establish_connection
        raise NetMikoAuthenticationException(msg)
    netmiko.ssh_exception.NetMikoAuthenticationException: Authentication failure: unable to connect cisco_ios 192.168.100.2:22
    Authentication failed.

Так как исключение возникает при получении результата, легко добавить
обработку исключений (файл netmiko\_threads\_submit\_exception.py):

.. code:: python

    from concurrent.futures import ThreadPoolExecutor, as_completed
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


    def threads_conn(function, devices, limit=2, command=''):
        all_results = {}
        with ThreadPoolExecutor(max_workers=limit) as executor:
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
        all_done = threads_conn(connect_ssh,
                                devices['routers'],
                                command='sh clock')
        pprint(all_done)

Результат выполнения:

::

    $ python netmiko_threads_submit_exception.py
    ===> 06:45:56.327892 Connection to device: 192.168.100.1
    ===> 06:45:56.328190 Connection to device: 192.168.100.2
    ===> 06:45:58.964806 Connection to device: 192.168.100.3
    Authentication failure: unable to connect cisco_ios 192.168.100.2:22
    Authentication failed.
    <=== 06:46:04.325812 Received result from device: 192.168.100.3
    <=== 06:46:11.731541 Received result from device: 192.168.100.1
    {'192.168.100.1': '*06:46:11.556 UTC Mon Aug 28 2017',
     '192.168.100.3': '*06:46:04.154 UTC Mon Aug 28 2017'}

Конечно, обработка исключения может выполняться и внутри функции
connect\_ssh, но это просто пример того, как можно работать с
исключениями при использовании future.

ProcessPoolExecutor
~~~~~~~~~~~~~~~~~~~

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

