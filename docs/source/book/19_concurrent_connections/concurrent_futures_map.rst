Метод map
~~~~~~~~~

Синтаксис метода:

.. code:: python

    map(func, *iterables, timeout=None)

Метод map - работает похоже на встроенную функцию map: применяет функцию
func к одному или более итерируемых объектов. При этом, каждый вызов
функции запускается в отдельном потоке/процессе. Метод map возвращает
итератор с результатами выполнения функции для каждого элемента итерируемого
объекта. Результаты расположены в том же порядке, что и элементы
в итерируемом объекте.

При работе с пулами потоков/процессов, создается определенное 
количество потоков/процессов и затем код выполняется в этих потоках.
Например, если при создании пула указано, что надо создать 5 потоков,
а функцию надо запустить для 10 разных устройств, подключение будет выполняться
сначала к первым пяти устройствам, а затем, по мере освобождения,
к остальным.

Пример использования функции map с ThreadPoolExecutor (файл
netmiko_threads_map_basics.py):

.. code:: python

    from datetime import datetime
    import time
    from itertools import repeat
    from concurrent.futures import ThreadPoolExecutor
    import logging

    import netmiko
    import yaml


    logging.getLogger('paramiko').setLevel(logging.WARNING)

    logging.basicConfig(
        format = '%(threadName)s %(name)s %(levelname)s: %(message)s',
        level=logging.INFO,
    )


    def send_show(device, show):
        start_msg = '===> {} Connection: {}'
        received_msg = '<=== {} Received:   {}'
        ip = device['ip']
        logging.info(start_msg.format(datetime.now().time(), ip))
        if ip == '192.168.100.1':
            time.sleep(5)

        with netmiko.ConnectHandler(**device) as ssh:
            ssh.enable()
            result = ssh.send_command(show)
            logging.info(received_msg.format(datetime.now().time(), ip))
            return result


    with open('devices.yaml') as f:
        devices = yaml.safe_load(f)

    with ThreadPoolExecutor(max_workers=3) as executor:
        result = executor.map(send_show, devices, repeat('sh clock'))
        for device, output in zip(devices, result):
            print(device['ip'], output)


Так как методу map надо передавать функцию, создана функция send_show,
которая подключается к оборудованию, передает указанную команду show и
возвращает результат с выводом команды.

.. code:: python

    def send_show(device, show):
        start_msg = '===> {} Connection: {}'
        received_msg = '<=== {} Received:   {}'
        ip = device['ip']
        logging.info(start_msg.format(datetime.now().time(), ip))
        if ip == '192.168.100.1':
            time.sleep(5)

        with netmiko.ConnectHandler(**device) as ssh:
            ssh.enable()
            result = ssh.send_command(show)
            logging.info(received_msg.format(datetime.now().time(), ip))
            return result

Функция send_show выводит лог сообщения в начале и в конце работы.
Это позволит определить когда функция отработала для конкретного устройства.
Также внутри функции указано, что при подключении к устройству с адресом 192.168.100.1,
надо сделать паузу на 5 секунд - таким образом маршрутизатор с этим адресом будет отрабатывать дольше.

Последние 4 строки кода отвечают за подключение к устройствам в отдельных потоках:

.. code:: python

    with ThreadPoolExecutor(max_workers=3) as executor:
        result = executor.map(send_show, devices, repeat('sh clock'))
        for device, output in zip(devices, result):
            print(device['ip'], output)

* ``with ThreadPoolExecutor(max_workers=3) as executor:`` - класс
  ThreadPoolExecutor инициируется в блоке with с указанием количества
  потоков. 
* ``result = executor.map(send_show, devices, repeat('sh clock'))`` - метод map похож на
   функцию map, но тут функция send_show вызывается в разных потоках. При
   этом в разных потоках функция будет вызываться с разными аргументами:

  * элементами итерируемого объекта devices и одной и той же командой sh clock.
  * так как вместо списка команд, тут используется только одна команда,
    ее надо каким-то образом повторять, чтобы метод map подставлял эту команду
    разным устройствам. Для этого используется функция repeat - она повторяет команду ровно столько
    раз, сколько запрашивает map
  
*  метод map возвращает генератор. В этом генераторе содержатся
   результаты выполнения функций. Результаты находятся в том же порядке,
   что и устройства в списке devices, поэтому для совмещения IP-адресов устройств
   и вывода команды используется функция zip.


Результат выполнения:

::

    $ python netmiko_threads_map_basics.py
    ThreadPoolExecutor-0_0 root INFO: ===> 08:28:55.950254 Connection: 192.168.100.1
    ThreadPoolExecutor-0_1 root INFO: ===> 08:28:55.963198 Connection: 192.168.100.2
    ThreadPoolExecutor-0_2 root INFO: ===> 08:28:55.970269 Connection: 192.168.100.3
    ThreadPoolExecutor-0_1 root INFO: <=== 08:29:11.968796 Received:   192.168.100.2
    ThreadPoolExecutor-0_2 root INFO: <=== 08:29:15.497324 Received:   192.168.100.3
    ThreadPoolExecutor-0_0 root INFO: <=== 08:29:16.854344 Received:   192.168.100.1
    192.168.100.1 *08:29:16.663 UTC Thu Jul 4 2019
    192.168.100.2 *08:29:11.744 UTC Thu Jul 4 2019
    192.168.100.3 *08:29:15.374 UTC Thu Jul 4 2019

Первые три сообщения указывают когда было выполнено подключение и к какому устройству:

::

    ThreadPoolExecutor-0_0 root INFO: ===> 08:28:55.950254 Connection: 192.168.100.1
    ThreadPoolExecutor-0_1 root INFO: ===> 08:28:55.963198 Connection: 192.168.100.2
    ThreadPoolExecutor-0_2 root INFO: ===> 08:28:55.970269 Connection: 192.168.100.3

Следующие три сообщения показывают время получения информации и завершения функции:

::

    ThreadPoolExecutor-0_1 root INFO: <=== 08:29:11.968796 Received:   192.168.100.2
    ThreadPoolExecutor-0_2 root INFO: <=== 08:29:15.497324 Received:   192.168.100.3
    ThreadPoolExecutor-0_0 root INFO: <=== 08:29:16.854344 Received:   192.168.100.1

Так как для первого устройства был добавлен sleep на 5 секунд, информация
с первого маршрутизатора фактически была получена позже всего.
Однако, так как метод map возвращает значения в том же порядке, что и устройства в списке device,
итоговый результат выглядит так:

::

    192.168.100.1 *08:29:16.663 UTC Thu Jul 4 2019
    192.168.100.2 *08:29:11.744 UTC Thu Jul 4 2019
    192.168.100.3 *08:29:15.374 UTC Thu Jul 4 2019

 
Обработка исключений с map
^^^^^^^^^^^^^^^^^^^^^^^^^^

Пример использования map с обработкой исключений:

.. code:: python

    from concurrent.futures import ThreadPoolExecutor
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
        level=logging.INFO,
    )


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
        with ThreadPoolExecutor(max_workers=2) as executor:
            result = executor.map(send_show, devices, repeat(command))
            for device, output in zip(devices, result):
                data[device['ip']] = output
        return data


    if __name__ == '__main__':
        with open('devices.yaml') as f:
            devices = yaml.safe_load(f)
        pprint(send_command_to_devices(devices, 'sh ip int br'))



Пример в целом аналогичен предыдущему, но в функции send_show появилась обработка
ошибки NetMikoAuthenticationException, а код, который запускал функцию send_show 
в потоках, теперь находится в функции send_command_to_devices.

При использовании метода map, обработку исключений лучше делать внутри функции, 
которая запускается в потоках, в данном случае это функция send_show.

