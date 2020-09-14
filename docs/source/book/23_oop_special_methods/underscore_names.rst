Подчеркивание в именах
----------------------

В Python подчеркивание в начале или в конце имени указывает на
специальные имена. Чаще всего это всего лишь договоренность, но иногда
это действительно влияет на поведение объекта.


Одно подчеркивание перед именем
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Одно подчеркивание перед именем метода указывает, что метод является 
внутренней особенностью реализации и его не стоит использовать напрямую.

Например, класс CiscoSSH использует paramiko для подключения к оборудованию:

.. code:: python

    import time
    import paramiko


    class CiscoSSH:
        def __init__(self, ip, username, password, enable, disable_paging=True):
            self.client = paramiko.SSHClient()
            self.client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
            self.client.connect(
                hostname=ip,
                username=username,
                password=password,
                look_for_keys=False,
                allow_agent=False)

            self.ssh = self.client.invoke_shell()
            self.ssh.send('enable\n')
            self.ssh.send(enable + '\n')
            if disable_paging:
                self.ssh.send('terminal length 0\n')
            time.sleep(1)
            self.ssh.recv(1000)

        def send_show_command(self, command):
            self.ssh.send(command + '\n')
            time.sleep(2)
            result = self.ssh.recv(5000).decode('ascii')
            return result


После создания экземпляра класса, доступен не только метод send_show_command,
но и атрибуты client и ssh (3 строка это подсказки по tab в ipython):

.. code:: python

    In [2]: r1 = CiscoSSH('192.168.100.1', 'cisco', 'cisco', 'cisco')

    In [3]: r1.
                client
                send_show_command()
                ssh


Если же необходимо указать, что client и ssh являются внутренними атрибутами,
которые нужны для работы класса, но не предназначены для пользователя,
надо поставить нижнее подчеркивание перед именем:

.. code:: python

    class CiscoSSH:
        def __init__(self, ip, username, password, enable, disable_paging=True):
            self._client = paramiko.SSHClient()
            self._client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

            self._client.connect(
                hostname=ip,
                username=username,
                password=password,
                look_for_keys=False,
                allow_agent=False)

            self._ssh = self._client.invoke_shell()
            self._ssh.send('enable\n')
            self._ssh.send(enable + '\n')
            if disable_paging:
                self._ssh.send('terminal length 0\n')
            time.sleep(1)
            self._ssh.recv(1000)

        def send_show_command(self, command):
            self._ssh.send(command + '\n')
            time.sleep(2)
            result = self._ssh.recv(5000).decode('ascii')
            return result


.. note::

    Часто такие методы и атрибуты называются приватными, но это не значит, 
    что методы и переменные недоступны пользователю.




Два подчеркивания перед именем
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Два подчеркивания перед именем метода используются не просто как
договоренность. Такие имена трансформируются в формат "имя класса + имя
метода". Это позволяет создавать уникальные методы и атрибуты классов.

Такое преобразование выполняется только в том случае, если в конце
менее двух подчеркиваний или нет подчеркиваний.

.. code:: python

    In [14]: class Switch(object):
        ...:     __quantity = 0
        ...:
        ...:     def __configure(self):
        ...:         pass
        ...:

    In [15]: dir(Switch)
    Out[15]:
    ['_Switch__configure', '_Switch__quantity', ...]

Хотя методы создавались без приставки ``_Switch``, она была добавлена.

Если создать подкласс, то метод ``__configure`` не перепишет метод
родительского класса Switch:

.. code:: python

    In [16]: class CiscoSwitch(Switch):
        ...:     __quantity = 0
        ...:     def __configure(self):
        ...:         pass
        ...:

    In [17]: dir(CiscoSwitch)
    Out[17]:
    ['_CiscoSwitch__configure', '_CiscoSwitch__quantity', '_Switch__configure', '_Switch__quantity', ...]

Два подчеркивания перед и после имени
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Таким образом обозначаются специальные переменные и методы.

Например, в модуле Python есть такие специальные переменные:

* ``__name__`` - эта переменная равна строке ``__main__``, когда скрипт
  запускается напрямую, и равна имени модуля, когда импортируется
* ``__file__`` - эта переменная равна имени скрипта, который был запущен
  напрямую, и равна полному пути к модулю, когда он импортируется

Переменная ``__name__`` чаще всего используется, чтобы указать, что
определенная часть кода должна выполняться, только когда модуль
выполняется напрямую:

.. code:: python


    def multiply(a, b):

        return a * b

    if __name__ == '__main__':
        print(multiply(3, 5))

А переменная ``__file__`` может быть полезна в определении текущего пути
к файлу скрипта:

.. code:: python

    import os

    print('__file__', __file__)
    print(os.path.abspath(__file__))

Вывод будет таким:

::

    __file__ example2.py
    /home/vagrant/repos/tests/example2.py

Кроме того, таким образом в Python обозначаются специальные методы. Эти
методы вызываются при использовании функций и операторов Python и
позволяют реализовать определенный функционал.

Как правило, такие методы не нужно вызывать напрямую. Но, например, при
создании своего класса может понадобиться описать такой метод, чтобы
объект поддерживал какие-то операции в Python.

Например, для того, чтобы можно было получить длину объекта, он должен
поддерживать метод ``__len__``.

