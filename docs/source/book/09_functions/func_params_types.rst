Типы параметров функции
-----------------------

При создании функции можно указать, какие аргументы нужно передавать
обязательно, а какие нет. Соответственно, функция может быть создана с:

* **обязательными параметрами**
* **необязательными параметрами** (опциональными, параметрами со значением по умолчанию)

.. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/09_function_params.png
   :align: center
   :class: only-light


.. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/09_function_params_dark.png
   :align: center
   :class: only-dark

Обязательные параметры
~~~~~~~~~~~~~~~~~~~~~~

**Обязательные параметры** - определяют, какие аргументы нужно передать
функции обязательно. При этом, их нужно передать ровно столько, сколько
указано параметров функции (нельзя указать большее или меньшее
количество)

Функция с обязательными параметрами (файл func\_params\_types.py):

.. code:: python

    def check_passwd(username, password):
        if len(password) < 8:
            print('Пароль слишком короткий')
            return False
        elif username in password:
            print('Пароль содержит имя пользователя')
            return False
        else:
            print(f'Пароль для пользователя {username} прошел все проверки')
            return True


Функция check_passwd ожидает два аргумента: username и password.

Функция проверяет пароль и возвращает False, если проверки не прошли и
True, если пароль прошел проверки:

.. code:: python

    In [2]: check_passwd('nata', '12345')
    Пароль слишком короткий
    Out[2]: False

    In [3]: check_passwd('nata', '12345lsdkjflskfdjsnata')
    Пароль содержит имя пользователя
    Out[3]: False

    In [4]: check_passwd('nata', '12345lsdkjflskfdjs')
    Пароль для пользователя nata прошел все проверки
    Out[4]: True


Необязательные параметры (параметры со значением по умолчанию)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

При создании функции можно указывать значение по умолчанию для параметра таким образом:
``def check_passwd(username, password, min_length=8)``. В этом случае, параметр min_length
указан со значением по умолчанию и может не передаваться при вызове функции.


Пример функции check_passwd с параметром со значением по умолчанию (файл func_check_passwd_optional_param.py):

.. code:: python

    def check_passwd(username, password, min_length=8):
        if len(password) < min_length:
            print('Пароль слишком короткий')
            return False
        elif username in password:
            print('Пароль содержит имя пользователя')
            return False
        else:
            print(f'Пароль для пользователя {username} прошел все проверки')
            return True


Так как у параметра min_length есть значение по умолчанию, соответствующий аргумент
можно не указывать при вызове функции, если значение по умолчанию подходит:

.. code:: python

    In [7]: check_passwd('nata', '12345')
    Пароль слишком короткий
    Out[7]: False


Если нужно поменять значение по умолчанию:

.. code:: python

    In [8]: check_passwd('nata', '12345', 3)
    Пароль для пользователя nata прошел все проверки
    Out[8]: True

