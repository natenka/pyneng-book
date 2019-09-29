Пример использования ключевых аргументов переменной длины и распаковки аргументов
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

С помощью аргументов переменной длины и распаковки аргументов можно
передавать аргументы между функциями. Посмотрим на примере.

Функция check_passwd (файл func_add_user_kwargs_example.py):

.. code:: python

    In [1]: def check_passwd(username, password, min_length=8, check_username=True):
       ...:     if len(password) < min_length:
       ...:         print('Пароль слишком короткий')
       ...:         return False
       ...:     elif check_username and username in password:
       ...:         print('Пароль содержит имя пользователя')
       ...:         return False
       ...:     else:
       ...:         print(f'Пароль для пользователя {username} прошел все проверки')
       ...:         return True
       ...:

Функция проверяет пароль и возвращает True, если пароль прошел проверки и False - если нет.

Вызов функции в ipython:

.. code:: python

    In [3]: check_passwd('nata', '12345', min_length=3)
    Пароль для пользователя nata прошел все проверки
    Out[3]: True

    In [4]: check_passwd('nata', '12345nata', min_length=3)
    Пароль содержит имя пользователя
    Out[4]: False

    In [5]: check_passwd('nata', '12345nata', min_length=3, check_username=False)
    Пароль для пользователя nata прошел все проверки
    Out[5]: True

    In [6]: check_passwd('nata', '12345nata', min_length=3, check_username=True)
    Пароль содержит имя пользователя
    Out[6]: False


Сделаем функцию add_user_to_users_file, которая запрашивает пароль
для указанного пользователя, проверяет его и запрашивает заново, если пароль не 
прошел проверки или записывает пользователя и пароль в файл, если пароль прошел проверки.

.. code:: python

    In [7]: def add_user_to_users_file(user, users_filename='users.txt'):
       ...:     while True:
       ...:         passwd = input(f'Введите пароль для пользователя {user}: ')
       ...:         if check_passwd(user, passwd):
       ...:             break
       ...:     with open(users_filename, 'a') as f:
       ...:         f.write(f'{user},{passwd}\n')
       ...:

    In [8]: add_user_to_users_file('nata')
    Введите пароль для пользователя nata: natasda
    Пароль слишком короткий
    Введите пароль для пользователя nata: natasdlajsl;fjd
    Пароль содержит имя пользователя
    Введите пароль для пользователя nata: salkfdjsalkdjfsal;dfj
    Пароль для пользователя nata прошел все проверки

    In [9]: cat users.txt
    nata,salkfdjsalkdjfsal;dfj

В таком варианте функции add_user_to_users_file нет возможности регулировать
минимальную длину пароля и то надо ли проверять наличие имени пользователя в пароле.
В следующем варианте функции add_user_to_users_file эти возможности добавлены:

.. code:: python

    In [5]: def add_user_to_users_file(user, users_filename='users.txt', min_length=8, check_username=True):
       ...:     while True:
       ...:         passwd = input(f'Введите пароль для пользователя {user}: ')
       ...:         if check_passwd(user, passwd, min_length, check_username):
       ...:             break
       ...:     with open(users_filename, 'a') as f:
       ...:         f.write(f'{user},{passwd}\n')
       ...:

    In [6]: add_user_to_users_file('nata', min_length=5)
    Введите пароль для пользователя nata: natas2342
    Пароль содержит имя пользователя
    Введите пароль для пользователя nata: dlfjgkd
    Пароль для пользователя nata прошел все проверки

Теперь при вызове функции можно указать параметр min_length или check_username.
Однако, пришлось повторить параметры функции check_passwd в определении функции add_user_to_users_file.
Это не очень хорошо и, когда параметров много, просто неудобно, особенно
если учитывать, что у функции check_passwd могут добавиться другие параметры.

Такая ситуация случается довольно часто и в Python есть распространенное решение этой задачи:
все аргументы для внутренней функции (в этом случае это check_passwd) будут приниматься в ``**kwargs``.
Затем, при вызове функции check_passwd они будут распаковываться в ключевые аргументы
тем же синтаксисом ``**kwargs``.

.. code:: python

    In [7]: def add_user_to_users_file(user, users_filename='users.txt', **kwargs):
       ...:     while True:
       ...:         passwd = input(f'Введите пароль для пользователя {user}: ')
       ...:         if check_passwd(user, passwd, **kwargs):
       ...:             break
       ...:     with open(users_filename, 'a') as f:
       ...:         f.write(f'{user},{passwd}\n')
       ...:

    In [8]: add_user_to_users_file('nata', min_length=5)
    Введите пароль для пользователя nata: alskfdjlksadjf
    Пароль для пользователя nata прошел все проверки

    In [9]: add_user_to_users_file('nata', min_length=5)
    Введите пароль для пользователя nata: 345
    Пароль слишком короткий
    Введите пароль для пользователя nata: 309487538
    Пароль для пользователя nata прошел все проверки


В таком варианте в функцию check_passwd можно добавлять аргументы
без необходимости дублировать их в функции add_user_to_users_file.
