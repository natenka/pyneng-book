Запись файлов
-------------

При записи, очень важно определиться с режимом открытия файла, чтобы
случайно его не удалить:

*  ``w`` - открыть файл для записи. Если файл существует, то его
   содержимое удаляется
*  ``a`` - открыть файл для дополнения записи. Данные добавляются в
   конец файла

При этом оба режима создают файл, если он не существует.

Для записи в файл используются такие методы:

*  ``write`` - записать в файл одну строку
*  ``writelines`` - позволяет передавать в качестве аргумента список строк

``write``
^^^^^^^^^^^

Метод ``write`` ожидает строку, для записи.

Для примера, возьмем список строк с конфигурацией:

.. code:: python

    In [1]: cfg_lines = ['!',
       ...:  'service timestamps debug datetime msec localtime show-timezone year',
       ...:  'service timestamps log datetime msec localtime show-timezone year',
       ...:  'service password-encryption',
       ...:  'service sequence-numbers',
       ...:  '!',
       ...:  'no ip domain lookup',
       ...:  '!',
       ...:  'ip ssh version 2',
       ...:  '!']

Открытие файла r2.txt в режиме для записи:

.. code:: python

    In [2]: f = open('r2.txt', 'w')

Преобразуем список команд в одну большую строку с помощью ``join``:

.. code:: python

    In [3]: cfg_lines_as_string = '\n'.join(cfg_lines)

    In [4]: cfg_lines_as_string
    Out[4]: '!\nservice timestamps debug datetime msec localtime show-timezone year\nservice timestamps log datetime msec localtime show-timezone year\nservice password-encryption\nservice sequence-numbers\n!\nno ip domain lookup\n!\nip ssh version 2\n!'

Запись строки в файл:

.. code:: python

    In [5]: f.write(cfg_lines_as_string)

Аналогично можно добавить строку вручную:

.. code:: python

    In [6]: f.write('\nhostname r2')

После завершения работы с файлом, его необходимо закрыть:

.. code:: python

    In [7]: f.close()

Так как ipython поддерживает команду cat, можно легко посмотреть
содержимое файла:

.. code:: python

    In [8]: cat r2.txt
    !
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    !
    no ip domain lookup
    !
    ip ssh version 2
    !
    hostname r2

``writelines``
^^^^^^^^^^^^^^^^

Метод ``writelines`` ожидает список строк, как аргумент.

Запись списка строк cfg_lines в файл:

.. code:: python

    In [1]: cfg_lines = ['!',
       ...:  'service timestamps debug datetime msec localtime show-timezone year',
       ...:  'service timestamps log datetime msec localtime show-timezone year',
       ...:  'service password-encryption',
       ...:  'service sequence-numbers',
       ...:  '!',
       ...:  'no ip domain lookup',
       ...:  '!',
       ...:  'ip ssh version 2',
       ...:  '!']

    In [9]: f = open('r2.txt', 'w')

    In [10]: f.writelines(cfg_lines)

    In [11]: f.close()

    In [12]: cat r2.txt
    !service timestamps debug datetime msec localtime show-timezone yearservice timestamps log datetime msec localtime show-timezone yearservice password-encryptionservice sequence-numbers!no ip domain lookup!ip ssh version 2!

В результате все строки из списка записались в одну строку файла, так
как в конце строк не было символа ``\n``.

Добавить перевод строки можно по-разному.
Например, можно просто обработать список в цикле:

.. code:: python

    In [13]: cfg_lines2 = []

    In [14]: for line in cfg_lines:
       ....:     cfg_lines2.append(line + '\n')
       ....:

    In [15]: cfg_lines2
    Out[15]:
    ['!\n',
     'service timestamps debug datetime msec localtime show-timezone year\n',
     'service timestamps log datetime msec localtime show-timezone year\n',
     'service password-encryption\n',
     'service sequence-numbers\n',
     '!\n',
     'no ip domain lookup\n',
     '!\n',
     'ip ssh version 2\n',

Если итоговый список записать заново в файл, то в нём уже
будут переводы строк:

.. code:: python

    In [18]: f = open('r2.txt', 'w')

    In [19]: f.writelines(cfg_lines2)

    In [20]: f.close()

    In [21]: cat r2.txt
    !
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    !
    no ip domain lookup
    !
    ip ssh version 2
    !

