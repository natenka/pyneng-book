Аргументы, которые можно передавать только как ключевые
-------------------------------------------------------

Аргументы, которые указаны после ``*`` можно передавать только как ключевые при
вызове функции.

.. note::

    Этот функционал доступен в любой версии Python3.

Например, в этой функции аргументы min_length и check_username можно передавать только как
ключевые.

.. code:: python

    def check_passwd(username, password, *, min_length=8, check_username=True):
        if len(password) < min_length:
            print('Пароль слишком короткий')
            return False
        elif check_username and username in password:
            print('Пароль содержит имя пользователя')
            return False
        else:
            print(f'Пароль для пользователя {username} прошел все проверки')
            return True


При передаче их как позиционных, возникнет исключение:

.. code:: python

    In [2]: check_passwd('nata', '12345', min_length=3)
    Пароль для пользователя nata прошел все проверки
    Out[2]: True

    In [3]: check_passwd('nata', '12345', 3)
    ---------------------------------------------------------------------------
    TypeError                                 Traceback (most recent call last)
    <ipython-input-3-4f346c9cf914> in <module>
    ----> 1 check_passwd('nata', '12345', 3)

    TypeError: check_passwd() takes 2 positional arguments but 3 were given

