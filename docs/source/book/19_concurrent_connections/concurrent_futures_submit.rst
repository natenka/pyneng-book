Метод submit и работа с futures
-------------------------------

Метод submit отличается от метода map:

* submit запускает в потоке только одну функцию
* с помощью submit можно запускать разные функции с разными несвязанными аргументами,
  а map надо обязательно запускать с итерируемым объектами в роли аргументов
* submit сразу возвращает результат, не дожидаясь выполнения функции
* submit возвращает специальный объект Future, который представляет
  выполнение функции.

  * submit возвращает Future для того чтобы вызов submit не блокировал
    код. Как только submit вернул Future, код может выполняться дальше.
    И как только запущены все функции в потоках, можно начинать запрашивать
    Future о том готовы ли результаты. Или воспользоваться специальной функцией
    as_completed, которая сама запрашивает результат, а код получает его
    по мере готовности

* submit можно передавать ключевые аргументы, а map только позиционные

Метод submit использует объект `Future <https://en.wikipedia.org/wiki/Futures_and_promises>`__ - это
объект, который представляет отложенное вычисление. Этот объект можно
запрашивать о состоянии (завершена работа или нет), можно получать
результаты или исключения, которые возникли в процессе работы.
Future не нужно создавать вручную, эти объекты создаются методом submit.

Пример запуска функции в потоках с помощью submit (файл
netmiko_threads_submit_basics.py):

.. code:: python

    from concurrent.futures import ThreadPoolExecutor
    from pprint import pprint
    from datetime import datetime
    import time
    import logging

    import yaml
    from netmiko import ConnectHandler, NetMikoAuthenticationException


    logging.getLogger("paramiko").setLevel(logging.WARNING)

    logging.basicConfig(
        format = '%(threadName)s %(name)s %(levelname)s: %(message)s',
        level=logging.INFO)


    def send_show(device_dict, command):
        start_msg = '===> {} Connection: {}'
        received_msg = '<=== {} Received: {}'
        ip = device_dict['ip']
        logging.info(start_msg.format(datetime.now().time(), ip))
        if ip == '192.168.100.1':
            time.sleep(5)

        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)
            logging.info(received_msg.format(datetime.now().time(), ip))
        return {ip: result}


    with open('devices.yaml') as f:
        devices = yaml.safe_load(f)

    with ThreadPoolExecutor(max_workers=2) as executor:
        future_list = []
        for device in devices:
            future = executor.submit(send_show, device, 'sh clock')
            future_list.append(future)
        for f in future_list:
            print(f.result())


Остальной код не изменился, поэтому разобраться надо только с блоком,
который запускает функцию send_show в потоках:

.. code:: python

    with ThreadPoolExecutor(max_workers=2) as executor:
        future_list = []
        for device in devices:
            future = executor.submit(send_show, device, 'sh clock')
            future_list.append(future)
        for f in future_list:
            print(f.result())

Теперь в блоке with два цикла: 

* ``future_list`` - это список объектов future:

  * для создания future используется функция submit 
  * ей как аргументы передаются: имя функции, которую надо выполнить, и ее аргументы 

* следующий цикл проходится по списку future и получает результат с помощью метода result


Результат выполнения:

::

    ThreadPoolExecutor-0_0 root INFO: ===> 12:13:39.194657 Connection: 192.168.100.1
    ThreadPoolExecutor-0_1 root INFO: ===> 12:13:39.195841 Connection: 192.168.100.2
    ThreadPoolExecutor-0_1 root INFO: <=== 12:13:40.024725 Received: 192.168.100.2
    ThreadPoolExecutor-0_1 root INFO: ===> 12:13:40.257218 Connection: 192.168.100.3
    ThreadPoolExecutor-0_1 root INFO: <=== 12:13:41.085220 Received: 192.168.100.3
    ThreadPoolExecutor-0_0 root INFO: <=== 12:13:45.025395 Received: 192.168.100.1
    {'192.168.100.1': '*12:13:45.017 UTC Tue Mar 16 2021'}
    {'192.168.100.2': '*12:13:40.019 UTC Tue Mar 16 2021'}
    {'192.168.100.3': '*12:13:41.077 UTC Tue Mar 16 2021'}


В этом случае результаты возвращаются в порядке создания Future, но при использовании
submit можно также использовать функцию as_completed, которая позволяет получать результаты
по мере того как функции завершают работу.

Пример запуска функции в потоках с помощью submit и as_completed
(полный код в файле netmiko_threads_submit_basics_as_completed.py):

.. code:: python

    from concurrent.futures import ThreadPoolExecutor, as_completed


    with ThreadPoolExecutor(max_workers=2) as executor:
        future_list = []
        for device in devices:
            future = executor.submit(send_show, device, 'sh clock')
            future_list.append(future)
        for f in as_completed(future_list):
            print(f.result())


Теперь цикл проходится по списку future с помощью функции as_completed. Эта функция
возвращает future только когда они завершили работу или были отменены.
При этом future возвращаются по мере завершения работы, не в порядке добавления в
список future_list.


.. note::

    Создание списка с future можно сделать с помощью list comprehensions:
    ``future_list = [executor.submit(send_show, device, 'sh clock') for device in devices]``

Результат выполнения:

::

    $ python netmiko_threads_submit_basics.py
    ThreadPoolExecutor-0_0 root INFO: ===> 17:32:59.088025 Connection: 192.168.100.1
    ThreadPoolExecutor-0_1 root INFO: ===> 17:32:59.094103 Connection: 192.168.100.2
    ThreadPoolExecutor-0_1 root INFO: <=== 17:33:11.639672 Received: 192.168.100.2
    {'192.168.100.2': '*17:33:11.429 UTC Thu Jul 4 2019'}
    ThreadPoolExecutor-0_1 root INFO: ===> 17:33:11.849132 Connection: 192.168.100.3
    ThreadPoolExecutor-0_0 root INFO: <=== 17:33:17.735761 Received: 192.168.100.1
    {'192.168.100.1': '*17:33:17.694 UTC Thu Jul 4 2019'}
    ThreadPoolExecutor-0_1 root INFO: <=== 17:33:23.230123 Received: 192.168.100.3
    {'192.168.100.3': '*17:33:23.188 UTC Thu Jul 4 2019'}


Обратите внимание, что порядок не сохраняется и зависит от того, какие
функции раньше завершили работу.

Future
~~~~~~

Пример запуска функции send_show с помощью submit и вывод информации о Future (обратите внимание на статус future в разные моменты времени):

.. code:: python

    In [1]: from concurrent.futures import ThreadPoolExecutor

    In [2]: from netmiko_threads_submit_futures import send_show

    In [3]: executor = ThreadPoolExecutor(max_workers=2)

    In [4]: f1 = executor.submit(send_show, r1, 'sh clock')
       ...: f2 = executor.submit(send_show, r2, 'sh clock')
       ...: f3 = executor.submit(send_show, r3, 'sh clock')
       ...:
    ThreadPoolExecutor-0_0 root INFO: ===> 17:53:19.656867 Connection: 192.168.100.1
    ThreadPoolExecutor-0_1 root INFO: ===> 17:53:19.657252 Connection: 192.168.100.2

    In [5]: print(f1, f2, f3, sep='\n')
    <Future at 0xb488e2ac state=running>
    <Future at 0xb488ef2c state=running>
    <Future at 0xb488e72c state=pending>

    ThreadPoolExecutor-0_1 root INFO: <=== 17:53:25.757704 Received: 192.168.100.2
    ThreadPoolExecutor-0_1 root INFO: ===> 17:53:25.869368 Connection: 192.168.100.3

    In [6]: print(f1, f2, f3, sep='\n')
    <Future at 0xb488e2ac state=running>
    <Future at 0xb488ef2c state=finished returned dict>
    <Future at 0xb488e72c state=running>

    ThreadPoolExecutor-0_0 root INFO: <=== 17:53:30.431207 Received: 192.168.100.1
    ThreadPoolExecutor-0_1 root INFO: <=== 17:53:31.636523 Received: 192.168.100.3

    In [7]: print(f1, f2, f3, sep='\n')
    <Future at 0xb488e2ac state=finished returned dict>
    <Future at 0xb488ef2c state=finished returned dict>
    <Future at 0xb488e72c state=finished returned dict>


Чтобы посмотреть на future, в скрипт добавлены несколько строк с выводом
информации (netmiko_threads_submit_futures.py):

.. code:: python

    from concurrent.futures import ThreadPoolExecutor, as_completed
    from pprint import pprint
    from datetime import datetime
    import time
    import logging

    import yaml
    from netmiko import ConnectHandler, NetMikoAuthenticationException


    logging.getLogger("paramiko").setLevel(logging.WARNING)

    logging.basicConfig(
        format = '%(threadName)s %(name)s %(levelname)s: %(message)s',
        level=logging.INFO)


    def send_show(device_dict, command):
        start_msg = '===> {} Connection: {}'
        received_msg = '<=== {} Received: {}'
        ip = device_dict['ip']
        logging.info(start_msg.format(datetime.now().time(), ip))
        if ip == '192.168.100.1':
            time.sleep(5)

        with ConnectHandler(**device_dict) as ssh:
            ssh.enable()
            result = ssh.send_command(command)
            logging.info(received_msg.format(datetime.now().time(), ip))
        return {ip: result}


    def send_command_to_devices(devices, command):
        data = {}
        with ThreadPoolExecutor(max_workers=2) as executor:
            future_list = []
            for device in devices:
                future = executor.submit(send_show, device, command)
                future_list.append(future)
                print('Future: {} for device {}'.format(future, device['ip']))
            for f in as_completed(future_list):
                result = f.result()
                print('Future done {}'.format(f))
                data.update(result)
        return data


    if __name__ == '__main__':
        with open('devices.yaml') as f:
            devices = yaml.safe_load(f)
        pprint(send_command_to_devices(devices, 'sh clock'))



Результат выполнения:

::

    $ python netmiko_threads_submit_futures.py
    Future: <Future at 0xb5ed938c state=running> for device 192.168.100.1
    ThreadPoolExecutor-0_0 root INFO: ===> 07:14:26.298007 Connection: 192.168.100.1
    Future: <Future at 0xb5ed96cc state=running> for device 192.168.100.2
    Future: <Future at 0xb5ed986c state=pending> for device 192.168.100.3
    ThreadPoolExecutor-0_1 root INFO: ===> 07:14:26.299095 Connection: 192.168.100.2
    ThreadPoolExecutor-0_1 root INFO: <=== 07:14:32.056003 Received: 192.168.100.2
    ThreadPoolExecutor-0_1 root INFO: ===> 07:14:32.164774 Connection: 192.168.100.3
    Future done <Future at 0xb5ed96cc state=finished returned dict>
    ThreadPoolExecutor-0_0 root INFO: <=== 07:14:36.714923 Received: 192.168.100.1
    Future done <Future at 0xb5ed938c state=finished returned dict>
    ThreadPoolExecutor-0_1 root INFO: <=== 07:14:37.577327 Received: 192.168.100.3
    Future done <Future at 0xb5ed986c state=finished returned dict>
    {'192.168.100.1': '*07:14:36.546 UTC Fri Jul 26 2019',
     '192.168.100.2': '*07:14:31.865 UTC Fri Jul 26 2019',
     '192.168.100.3': '*07:14:37.413 UTC Fri Jul 26 2019'}


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
    ...
      File "/home/vagrant/venv/py3_convert/lib/python3.6/site-packages/netmiko/base_connection.py", line 500, in establish_connection
        raise NetMikoAuthenticationException(msg)
    netmiko.ssh_exception.NetMikoAuthenticationException: Authentication failure: unable to connect cisco_ios 192.168.100.2:22
    Authentication failed.

Так как исключение возникает при получении результата, легко добавить
обработку исключений (файл netmiko_threads_submit_exception.py):


.. code:: python

    from concurrent.futures import ThreadPoolExecutor, as_completed
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
        with ThreadPoolExecutor(max_workers=2) as executor:
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

    $ python netmiko_threads_submit_exception.py
    ThreadPoolExecutor-0_0 root INFO: ===> 07:21:21.190544 Connection: 192.168.100.1
    ThreadPoolExecutor-0_1 root INFO: ===> 07:21:21.191429 Connection: 192.168.100.2
    ThreadPoolExecutor-0_1 root INFO: ===> 07:21:23.672425 Connection: 192.168.100.3
    Authentication failure: unable to connect cisco_ios 192.168.100.2:22
    Authentication failed.
    ThreadPoolExecutor-0_1 root INFO: <=== 07:21:29.095289 Received: 192.168.100.3
    ThreadPoolExecutor-0_0 root INFO: <=== 07:21:31.607635 Received: 192.168.100.1
    {'192.168.100.1': '*07:21:31.436 UTC Fri Jul 26 2019',
     '192.168.100.3': '*07:21:28.930 UTC Fri Jul 26 2019'}


Конечно, обработка исключения может выполняться и внутри функции
send_show, но это просто пример того, как можно работать с
исключениями при использовании future.

