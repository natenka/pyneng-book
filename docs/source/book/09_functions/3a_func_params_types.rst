Типы параметров функции
-----------------------

При создании функции можно указать, какие аргументы нужно передавать
обязательно, а какие нет.

Соответственно, функция может быть создана с параметрами:

* **обязательными**
* **необязательными** (опциональными, параметрами со значением по умолчанию)

Обязательные параметры
~~~~~~~~~~~~~~~~~~~~~~

**Обязательные параметры** - определяют, какие аргументы нужно передать
функции обязательно. При этом, их нужно передать ровно столько, сколько
указано параметров функции (нельзя указать большее или меньшее
количество аргументов)

Функция с обязательными параметрами (файл func\_params\_types.py):

.. code:: python

    In [1]: def cfg_to_list(cfg_file, delete_exclamation):
      ....:     result = []
      ....:     with open( cfg_file ) as f:
      ....:         for line in f:
      ....:             if delete_exclamation and line.startswith('!'):
      ....:                 pass
      ....:             else:
      ....:                 result.append(line.rstrip())
      ....:     return result

Функция cfg\_to\_list ожидает два аргумента: cfg\_file и
delete\_exclamation.

Внутри она открывает файл cfg\_file для чтения, проходится по всем
строкам и, если аргумент delete\_exclamation истина и строка начинается
с восклицательного знака, строка пропускается. Оператор ``pass``
означает, что ничего не выполняется.

Во всех остальных случаях в строке справа удаляются символы перевода
строки, и строка добавляется в список result.

Пример вызова функции:

.. code:: python

    In [2]: cfg_to_list('r1.txt', True)
    Out[2]:
    ['service timestamps debug datetime msec localtime show-timezone year',
     'service timestamps log datetime msec localtime show-timezone year',
     'service password-encryption',
     'service sequence-numbers',
     'no ip domain lookup',
     'ip ssh version 2']

Так как аргументу delete\_exclamation передано значение True, в итоговом
списке нет строк с восклицательными знаками.

Вызов функции со значением False для аргумента delete\_exclamation:

.. code:: python

    In [3]: cfg_to_list('r1.txt', False)
    Out[3]:
    ['!',
     'service timestamps debug datetime msec localtime show-timezone year',
     'service timestamps log datetime msec localtime show-timezone year',
     'service password-encryption',
     'service sequence-numbers',
     '!',
     'no ip domain lookup',
     '!',
     'ip ssh version 2',
     '!']

Необязательные параметры (параметры со значением по умолчанию)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

При создании функции можно указывать значение по умолчанию для параметра
(файл func\_params\_types.py):

.. code:: python

    In [4]: def cfg_to_list(cfg_file, delete_exclamation=True):
      ....:     result = []
      ....:     with open( cfg_file ) as f:
      ....:         for line in f:
      ....:             if delete_exclamation and line.startswith('!'):
      ....:                 pass
      ....:             else:
      ....:                 result.append(line.rstrip())
      ....:     return result
      ....:

Так как теперь у параметра delete\_exclamation значение по умолчанию
равно True, соответствующий аргумент можно не указывать при вызове
функции, если значение по умолчанию подходит:

.. code:: python

    In [5]: cfg_to_list('r1.txt')
    Out[5]:
    ['service timestamps debug datetime msec localtime show-timezone year',
     'service timestamps log datetime msec localtime show-timezone year',
     'service password-encryption',
     'service sequence-numbers',
     'no ip domain lookup',
     'ip ssh version 2']

Но можно и указать, если нужно поменять значение по умолчанию:

.. code:: python

    In [6]: cfg_to_list('r1.txt', False)
    Out[6]:
    ['!',
     'service timestamps debug datetime msec localtime show-timezone year',
     'service timestamps log datetime msec localtime show-timezone year',
     'service password-encryption',
     'service sequence-numbers',
     '!',
     'no ip domain lookup',
     '!',
     'ip ssh version 2',
     '!']

