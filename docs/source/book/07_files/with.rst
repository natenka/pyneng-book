Конструкция with
----------------

Конструкция with называется менеджер контекста.

В Python существует более удобный способ работы с файлами, чем те,
которые использовались до сих пор - конструкция ``with``:

.. code:: python

    In [1]: with open('r1.txt', 'r') as f:
      ....:     for line in f:
      ....:         print(line)
      ....:
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

Кроме того, конструкция ``with`` гарантирует закрытие файла
автоматически.

Обратите внимание на то, как считываются строки файла:

.. code:: python

    for line in f:
        print(line)

Когда с файлом нужно работать построчно, лучше использовать такой
вариант.

В предыдущем выводе, между строками файла были лишние пустые строки, так
как print добавляет ещё один перевод строки.

Чтобы избавиться от этого, можно использовать метод ``rstrip``:

.. code:: python

    In [2]: with open('r1.txt', 'r') as f:
      ....:     for line in f:
      ....:         print(line.rstrip())
      ....:
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

    In [3]: f.closed
    Out[3]: True

И конечно же, с конструкцией ``with`` можно использовать не только
такой построчный вариант считывания, все методы, которые рассматривались
до этого, также работают:

.. code:: python

    In [4]: with open('r1.txt', 'r') as f:
      ....:     print(f.read())
      ....:
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

Открытие двух файлов
~~~~~~~~~~~~~~~~~~~~

Иногда нужно работать одновременно с двумя файлами. Например, надо
записать некоторые строки из одного файла, в другой.

В таком случае, в блоке with можно открывать два файла таким образом:

.. code:: python

    In [5]: with open('r1.txt') as src, open('result.txt', 'w') as dest:
       ...:     for line in src:
       ...:         if line.startswith('service'):
       ...:             dest.write(line)
       ...:

    In [6]: cat result.txt
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers

Это равнозначно таким двум блокам with:

.. code:: python

    In [7]: with open('r1.txt') as src:
       ...:     with open('result.txt', 'w') as dest:
       ...:         for line in src:
       ...:             if line.startswith('service'):
       ...:                 dest.write(line)
       ...:

