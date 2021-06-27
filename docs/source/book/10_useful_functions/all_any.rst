Функция all
-----------

Функция ``all`` возвращает True, если все элементы истинные (или объект
пустой).

.. code:: python

    In [1]: all([False, True, True])
    Out[1]: False

    In [2]: all([True, True, True])
    Out[2]: True

    In [3]: all([])
    Out[3]: True

Например, с помощью ``all`` можно проверить, все ли октеты в IP-адресе
являются числами:

.. code:: python

    In [4]: ip = '10.0.1.1'

    In [5]: all(i.isdigit() for i in ip.split('.'))
    Out[5]: True

    In [6]: all(i.isdigit() for i in '10.1.1.a'.split('.'))
    Out[6]: False

Функция any
-----------

Функция ``any`` возвращает True, если хотя бы один элемент истинный.

.. code:: python

    In [7]: any([False, True, True])
    Out[7]: True

    In [8]: any([False, False, False])
    Out[8]: False

    In [9]: any([])
    Out[9]: False

    In [10]: any(i.isdigit() for i in '10.1.1.a'.split('.'))
    Out[10]: True

Например, с помощью any, можно заменить функцию ignore_command:

.. code:: python

    def ignore_command(command):
        '''
        Функция проверяет содержится ли в команде слово из списка ignore.
        * command - строка. Команда, которую надо проверить
        * Возвращает True, если в команде содержится слово из списка ignore, False - если нет
        '''
        ignore = ['duplex', 'alias', 'Current configuration']

        for word in ignore:
            if word in command:
                return True
        return False

На такой вариант:

.. code:: python

    def ignore_command(command):
        '''
        Функция проверяет содержится ли в команде слово из списка ignore.
        command - строка. Команда, которую надо проверить
        Возвращает True, если в команде содержится слово из списка ignore, False - если нет
        '''
        ignore = ['duplex', 'alias', 'Current configuration']

        return any([word in command for word in ignore])

