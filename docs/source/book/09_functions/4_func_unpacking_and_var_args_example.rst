Пример использования ключевых аргументов переменной длины и распаковки аргументов
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

С помощью аргументов переменной длины и распаковки аргументов можно
передавать аргументы между функциями. Посмотрим на примере.

Функция config\_to\_list (файл kwargs\_example.py):

.. code:: python

    def config_to_list(cfg_file, delete_excl=True,
                       delete_empty=True, strip_end=True):
        result = []
        with open(cfg_file) as f:
            for line in f:
                if strip_end:
                    line = line.rstrip()
                if delete_empty and not line:
                    pass
                elif delete_excl and line.startswith('!'):
                    pass
                else:
                    result.append(line)
        return result

Функция берет файл с конфигурацией, убирает часть строк и возвращает
остальные строки как список.

Вызов функции в ipython:

.. code:: python

    In [1]: config_to_list('r1.txt')
    Out[1]:
    ['service timestamps debug datetime msec localtime show-timezone year',
     'service timestamps log datetime msec localtime show-timezone year',
     'service password-encryption',
     'service sequence-numbers',
     'no ip domain lookup',
     'ip ssh version 2']

По умолчанию из конфигурации убираются пустые строки, перевод строки в
конце строк и строки, которые начинаются на знак восклицания.

Вызов функции со значением ``delete_empty=False``:

.. code:: python

    In [2]: config_to_list('r1.txt', delete_empty=False)
    Out[2]:
    ['service timestamps debug datetime msec localtime show-timezone year',
     'service timestamps log datetime msec localtime show-timezone year',
     'service password-encryption',
     'service sequence-numbers',
     'no ip domain lookup',
     '',
     '',
     'ip ssh version 2']

Теперь пустые строки появились в списке.

Сделаем 'оберточную' функцию clear\_cfg\_and\_write\_to\_file, которая
берет файл конфигурации с помощью функции config\_to\_list, удаляет
лишние строки и затем записывает строки в указанный файл.

Но, при этом, мы не хотим терять возможность управлять тем, какие строки
будут отброшены. То есть, необходимо, чтобы функция
clear\_cfg\_and\_write\_to\_file поддерживала те же параметры, что и
функция config\_to\_list.

Конечно, можно просто продублировать все параметры функции и передать их
в функцию config\_to\_list:

.. code:: python

    def clear_cfg_and_write_to_file(cfg, to_file, delete_excl=True,
                                    delete_empty=True, strip_end=True):

        cfg_as_list = config_to_list(cfg, delete_excl=delete_excl,
                        delete_empty=delete_empty, strip_end=strip_end)
        with open(to_file, 'w') as f:
            f.write('\n'.join(cfg_as_list))

Но, если воспользоваться возможностью Python принимать аргументы
переменной длины, можно сделать функцию clear\_cfg\_and\_write\_to\_file
такой:

.. code:: python

    def clear_cfg_and_write_to_file(cfg, to_file, **kwargs):
        cfg_as_list = config_to_list(cfg, **kwargs)
        with open(to_file, 'w') as f:
            f.write('\n'.join(cfg_as_list))

В функции clear\_cfg\_and\_write\_to\_file явно прописаны её аргументы,
а всё остальное попадет в переменную ``kwargs``. Затем переменная
``kwargs`` передается как аргумент в функцию config\_to\_list. Но, так
как переменная ``kwargs`` - это словарь, её надо распаковать при
передаче функции config\_to\_list.

Так функция clear\_cfg\_and\_write\_to\_file выглядит проще и понятней.
И, главное, в таком варианте в функцию config\_to\_list можно добавлять
аргументы без необходимости дублировать их в функции
clear\_cfg\_and\_write\_to\_file.

    В этом примере ``**kwargs`` используется и для того, чтобы указать,
    что функция clear\_cfg\_and\_write\_to\_file может принимать
    аргументы переменной длины, и для того, чтобы 'распаковать' словарь
    kwargs, когда мы передаем его в функцию config\_to\_list.
