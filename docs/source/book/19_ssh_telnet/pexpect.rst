Модуль pexpect
--------------

Модуль pexpect позволяет автоматизировать интерактивные подключения,
такие как:

* telnet 
* ssh 
* ftp

.. note::

    Pexpect - это реализация expect на Python.

Для начала, модуль pexpect нужно установить:

::

    pip install pexpect


Логика работы pexpect такая: 

* запускается какая-то программа 
* pexpect ожидает определенный вывод (приглашение, запрос пароля и подобное) 
* получив вывод, он отправляет команды/данные 
* последние два действия повторяются столько, сколько нужно

При этом сам pexpect не реализует различные утилиты, а использует уже
готовые.

``pexpect.spawn``
~~~~~~~~~~~~~~~~~

Класс ``spawn`` позволяет взаимодействовать с вызванной
программой, отправляя данные и ожидая ответ.

Например, таким образом можно инициировать соединение SSH:

.. code:: python

    In [5]: ssh = pexpect.spawn('ssh cisco@192.168.100.1')

После выполнения этой строки, подключение готово. Теперь необходимо
указать какую строку ожидать. В данном случае, надо дождаться запроса о
пароле:

.. code:: python

    In [6]: ssh.expect('[Pp]assword')
    Out[6]: 0

Обратите внимание как описана строка, которую ожидает pexpect:
``[Pp]assword``. Это регулярное выражение, которое описывает строку
password или Password. То есть, методу expect можно передавать
регулярное выражение как аргумент.

Метод expect вернул число 0 в результате работы. Это число указывает,
что совпадение было найдено и что это элемент с индексом ноль. Индекс
тут фигурирует из-за того, что expect можно передавать список строк.
Например, можно передать список с двумя элементами:

.. code:: python

    In [7]: ssh = pexpect.spawn('ssh cisco@192.168.100.1')

    In [8]: ssh.expect(['password', 'Password'])
    Out[8]: 1

Обратите внимание, что теперь возвращается 1. Это значит, что
совпадением было слово Password.

Теперь можно отправлять пароль. Для этого используется команда sendline:

.. code:: python

    In [9]: ssh.sendline('cisco')
    Out[9]: 6

Команда sendline отправляет строку, автоматически добавляет к ней
перевод строки на основе значения os.linesep, а затем возвращает число
указывающее сколько байт было записано.

.. note::

    В pexpect есть несколько вариантов отправки команд, не только
    sendline.

Для того чтобы попасть в режим enable цикл expect-sendline повторяется:

.. code:: python

    In [10]: ssh.expect('[>#]')
    Out[10]: 0

    In [11]: ssh.sendline('enable')
    Out[11]: 7

    In [12]: ssh.expect('[Pp]assword')
    Out[12]: 0

    In [13]: ssh.sendline('cisco')
    Out[13]: 6

    In [14]: ssh.expect('[>#]')
    Out[14]: 0

Теперь можно отправлять команду:

.. code:: python

    In [15]: ssh.sendline('sh ip int br')
    Out[15]: 13

После отправки команды, pexpect надо указать до какого момента считать
вывод. Указываем, что считать надо до #:

.. code:: python

    In [16]: ssh.expect('#')
    Out[16]: 0

Вывод команды находится в атрибуте before:

.. code:: python

    In [17]: ssh.before
    Out[17]: b'sh ip int br\r\nInterface                  IP-Address      OK? Method Status                Protocol\r\nEthernet0/0                192.168.100.1   YES NVRAM  up                    up      \r\nEthernet0/1                192.168.200.1   YES NVRAM  up                    up      \r\nEthernet0/2                19.1.1.1        YES NVRAM  up                    up      \r\nEthernet0/3                192.168.230.1   YES NVRAM  up                    up      \r\nEthernet0/3.100            10.100.0.1      YES NVRAM  up                    up      \r\nEthernet0/3.200            10.200.0.1      YES NVRAM  up                    up      \r\nEthernet0/3.300            10.30.0.1       YES NVRAM  up                    up      \r\nR1'

Так как результат выводится в виде последовательности байтов, надо
конвертировать ее в строку:

.. code:: python

    In [18]: show_output = ssh.before.decode('utf-8')

    In [19]: print(show_output)
    sh ip int br
    Interface                  IP-Address      OK? Method Status                Protocol
    Ethernet0/0                192.168.100.1   YES NVRAM  up                    up
    Ethernet0/1                192.168.200.1   YES NVRAM  up                    up
    Ethernet0/2                19.1.1.1        YES NVRAM  up                    up
    Ethernet0/3                192.168.230.1   YES NVRAM  up                    up
    Ethernet0/3.100            10.100.0.1      YES NVRAM  up                    up
    Ethernet0/3.200            10.200.0.1      YES NVRAM  up                    up
    Ethernet0/3.300            10.30.0.1       YES NVRAM  up                    up
    R1

Завершается сессия вызовом метода close:

.. code:: python

    In [20]: ssh.close()

Специальные символы в shell
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Pexpect не интерпретирует специальные символы shell, такие как ``>``,
``|``, ``*``.

Для того, чтобы, например, команда ``ls -ls | grep SUMMARY`` отработала,
нужно запустить shell таким образом:

.. code:: python

    In [1]: import pexpect

    In [2]: p = pexpect.spawn('/bin/bash -c "ls -ls | grep pexpect"')

    In [3]: p.expect(pexpect.EOF)
    Out[3]: 0

    In [4]: print(p.before)
    b'4 -rw-r--r-- 1 vagrant vagrant 3203 Jul 14 07:15 1_pexpect.py\r\n'

    In [5]: print(p.before.decode('utf-8'))
    4 -rw-r--r-- 1 vagrant vagrant 3203 Jul 14 07:15 1_pexpect.py

pexpect.EOF
~~~~~~~~~~~

В предыдущем примере встретилось использование pexpect.EOF.

.. note::

    EOF (end of file) — конец файла

Это специальное значение, которое позволяет отреагировать на завершение
исполнения команды или сессии, которая была запущена в spawn.

При вызове команды ``ls -ls`` pexpect не получает интерактивный сеанс.
Команда выполняется и всё, на этом завершается её работа.

Поэтому если запустить её и указать в expect приглашение, возникнет
ошибка:

.. code:: python

    In [5]: p = pexpect.spawn('/bin/bash -c "ls -ls | grep SUMMARY"')

    In [6]: p.expect('nattaur')
    ---------------------------------------------------------------------------
    EOF                                       Traceback (most recent call last)
    <ipython-input-9-9c71777698c2> in <module>()
    ----> 1 p.expect('nattaur')
    ...

Если передать в expect EOF, ошибки не будет.

Метод pexpect.expect
~~~~~~~~~~~~~~~~~~~~

В pexpect.expect как шаблон может использоваться: 

* регулярное выражение 
* EOF - этот шаблон позволяет среагировать на исключение EOF
* TIMEOUT - исключение timeout (по умолчанию значение timeout = 30 секунд) 
* compiled re

Еще одна очень полезная возможность pexpect.expect: можно передавать не
одно значение, а список.

Например:

.. code:: python

    In [7]: p = pexpect.spawn('/bin/bash -c "ls -ls | grep netmiko"')

    In [8]: p.expect(['py3_convert', pexpect.TIMEOUT, pexpect.EOF])
    Out[8]: 2

Тут несколько важных моментов: 

* когда pexpect.expect вызывается со списком, можно указывать разные ожидаемые строки 
* кроме строк, можно указывать исключения 
* pexpect.expect возвращает номер элемента списка, который сработал 

  * в данном случае номер 2, так как исключение EOF находится в списке под номером два 

* за счет такого формата можно делать ответвления в программе, 
  в зависимости от того, с каким элементом было совпадение

Пример использования pexpect
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Пример использования pexpect для подключения к оборудованию и передачи
команды show (файл 1_pexpect.py):

.. code:: python

    import pexpect
    import re
    from pprint import pprint


    def send_show_command(ip, username, password, enable, commands, prompt="#"):
        with pexpect.spawn(f"ssh {username}@{ip}", timeout=10, encoding="utf-8") as ssh:
            ssh.expect("[Pp]assword")
            ssh.sendline(password)
            enable_status = ssh.expect([">", "#"])
            if enable_status == 0:
                ssh.sendline("enable")
                ssh.expect("[Pp]assword")
                ssh.sendline(enable)
                ssh.expect(prompt)

            ssh.sendline("terminal length 0")
            ssh.expect(prompt)

            result = {}
            for command in commands:
                ssh.sendline(command)
                match = ssh.expect([prompt, pexpect.TIMEOUT, pexpect.EOF])
                if match == 1:
                    print(
                        f"Символ {prompt} не найден в выводе. Полученный вывод записан в словарь"
                    )
                if match == 2:
                    print("Соединение разорвано со стороны сервера")
                    return result
                else:
                    output = ssh.before
                    result[command] = output.replace("\r\n", "\n")
            return result


    if __name__ == "__main__":
        devices = ["192.168.100.1", "192.168.100.2", "192.168.100.3"]
        commands = ["sh clock", "sh int desc"]
        for ip in devices:
            result = send_show_command(ip, "cisco", "cisco", "cisco", commands)
            pprint(result, width=120)

Эта часть функции отвечает за переход в режим enable:

.. code:: python

    enable_status = ssh.expect([">", "#"])
    if enable_status == 0:
        ssh.sendline("enable")
        ssh.expect("[Pp]assword")
        ssh.sendline(enable)
        ssh.expect(prompt)

Если ``ssh.expect([">", "#"])`` возвращает индекс 0, значит при подключении не было
автоматического перехода в режим enable и его надо выполнить.
Если возвращается индекс 1 - значит мы уже находимся в режиме enable, например, потому что
на оборудовании настроено privilege 15.

Еще один интересный момент в функции:

.. code:: python

    for command in commands:
        ssh.sendline(command)
        match = ssh.expect([prompt, pexpect.TIMEOUT, pexpect.EOF])
        if match == 1:
            print(
                f"Символ {prompt} не найден в выводе. Полученный вывод записан в словарь"
            )
        if match == 2:
            print("Соединение разорвано со стороны сервера")
            return result
        else:
            output = ssh.before
            result[command] = output.replace("\r\n", "\n")
    return result

Тут по очереди отправляются команды и expect ждет три варианта: приглашение, таймаут или EOF.
Если метод expect не дождался ``#``, будет возвращено значение 1 и в этом случае выводится сообщение,
что символ не найден. При этом, и когда совпадение найдено и когда был таймаут, полученный вывод
записывается в словарь. Таким образом можно увидеть, что было получено с устройства, даже
если приглашение не найдено.

Вывод при запуске скрипта:

::

    {'sh clock': 'sh clock\n*13:13:47.525 UTC Sun Jul 19 2020\n',
     'sh int desc': 'sh int desc\n'
                    'Interface                      Status         Protocol Description\n'
                    'Et0/0                          up             up       \n'
                    'Et0/1                          up             up       \n'
                    'Et0/2                          up             up       \n'
                    'Et0/3                          up             up       \n'
                    'Lo22                           up             up       \n'
                    'Lo33                           up             up       \n'
                    'Lo45                           up             up       \n'
                    'Lo55                           up             up       \n'}
    {'sh clock': 'sh clock\n*13:13:50.450 UTC Sun Jul 19 2020\n',
     'sh int desc': 'sh int desc\n'
                    'Interface                      Status         Protocol Description\n'
                    'Et0/0                          up             up       \n'
                    'Et0/1                          up             up       \n'
                    'Et0/2                          admin down     down     \n'
                    'Et0/3                          admin down     down     \n'
                    'Lo0                            up             up       \n'
                    'Lo9                            up             up       \n'
                    'Lo19                           up             up       \n'
                    'Lo33                           up             up       \n'
                    'Lo100                          up             up       \n'}
    {'sh clock': 'sh clock\n*13:13:53.360 UTC Sun Jul 19 2020\n',
     'sh int desc': 'sh int desc\n'
                    'Interface                      Status         Protocol Description\n'
                    'Et0/0                          up             up       \n'
                    'Et0/1                          up             up       \n'
                    'Et0/2                          admin down     down     \n'
                    'Et0/3                          admin down     down     \n'
                    'Lo33                           up             up       \n'}

Работа с pexpect без отключения постраничного вывода команд
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Иногда вывод команды сильно большой и его не получается полностью считать или оборудование не
дает возможность отключить постраничный вывод. В этом случае необходим немного другой подход.

.. note::

    Эта же задача будет повторяться и для других модулей этого раздела.


Пример использования pexpect для работы с постраничным выводом команд
show (файл 1_pexpect_more.py):

.. code:: python

    import pexpect
    import re
    from pprint import pprint


    def send_show_command(ip, username, password, enable, command, prompt="#"):
        with pexpect.spawn(f"ssh {username}@{ip}", timeout=10, encoding="utf-8") as ssh:
            ssh.expect("[Pp]assword")
            ssh.sendline(password)
            enable_status = ssh.expect([">", "#"])
            if enable_status == 0:
                ssh.sendline("enable")
                ssh.expect("[Pp]assword")
                ssh.sendline(enable)
                ssh.expect(prompt)

            ssh.sendline(command)
            output = ""

            while True:
                match = ssh.expect([prompt, "--More--", pexpect.TIMEOUT])
                page = ssh.before.replace("\r\n", "\n")
                page = re.sub(" +\x08+ +\x08+", "\n", page)
                output += page
                if match == 0:
                    break
                elif match == 1:
                    ssh.send(" ")
                else:
                    print("Ошибка: timeout")
                    break
            output = re.sub("\n +\n", "\n", output)
            return output


    if __name__ == "__main__":
        devices = ["192.168.100.1", "192.168.100.2", "192.168.100.3"]
        for ip in devices:
            result = send_show_command(ip, "cisco", "cisco", "cisco", "sh run")
            with open(f"{ip}_result.txt", "w") as f:
                f.write(result)


Теперь после отправки команды, метод expect ждет еще один вариант ``--More--`` - признак,
что дальше идет еще одна страница. Так как заранее не известно сколько именно страниц будет в выводе,
чтение выполняется в цикле ``while True``. Цикл прерывается если встретилось приглашение ``#``
или в течение 10 секунд не появилось приглашение или ``--More--``.

Если встретилось ``--More--``, страницы еще не закончились и надо пролистнуть следующую.
В Cisco для этого надо нажать пробел (без перевода строки). Поэтому тут используется метод send,
а не sendline - sendline автоматически добавляет перевод строки.

Эта строка ``page = re.sub(" +\x08+ +\x08+", "\n", page)`` удаляет backspace символы, которые находятся вокруг ``--More--`` чтобы они не попали в итоговый вывод.
