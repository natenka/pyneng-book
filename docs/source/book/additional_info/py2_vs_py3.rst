Отличия Python 2.7 и Python 3.6
-------------------------------

`Unicode <../16_unicode/>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

В Python 2.7 было два типа строк: str и unicode:

.. code:: python

    In [1]: line = 'test'

    In [2]: line2 = u'тест'

В Python 3 строка - это тип str, но, кроме этого, в Python 3 появился
тип bytes:

.. code:: python

    In [3]: line = 'тест'

    In [4]: line.encode('utf-8')
    Out[4]: b'\xd1\x82\xd0\xb5\xd1\x81\xd1\x82'

    In [5]: byte_str = b'test'

`Функция print <../10_useful_functions/print.html>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

В Python 2.7 print был оператором:

.. code:: python

    In [6]: print 1, 'test'
    1 test

В Python 3 `print - функция <../10_useful_functions/print.md>`__:

.. code:: python

    In [7]: print(1, 'test')
    1 test

В Python 2.7 можно брать аргументы в скобки, но от этого print не
становится функцией и, кроме того, print возвращает другой результат
(кортеж):

.. code:: python

    In [8]: print(1, 'test')
    (1, 'test')

В Python 3, использование синтаксиса Python 2.7 приведет к ошибке:

.. code:: python

    In [9]: print 1, 'test'
      File "<ipython-input-2-328abb6b105d>", line 1
        print 1, 'test'
              ^
    SyntaxError: Missing parentheses in call to 'print'

`input вместо raw_input <../05_basic_scripts/2_user_input.html>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

В Python 2.7 для получения информации от пользователя в виде строки
использовалась функция raw_input:

.. code:: python

    In [10]: number = raw_input('Number: ')
    Number: 55

    In [11]: number
    Out[11]: '55'

В Python 3 используется input:

.. code:: python

    In [12]: number = input('Number: ')
    Number: 55

    In [13]: number
    Out[13]: '55'

`range вместо xrange <../10_useful_functions/range.html>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

В Python 2.7 были две функции

* range - возвращает список
* xrange - возвращает итератор

Пример range и xrange в Python 2.7:

.. code:: python

    In [14]: range(5)
    Out[14]: [0, 1, 2, 3, 4]

    In [15]: xrange(5)
    Out[15]: xrange(5)

    In [16]: list(xrange(5))
    Out[16]: [0, 1, 2, 3, 4]

В Python 3 есть только функция range, и она возвращает итератор:

.. code:: python

    In [17]: range(5)
    Out[17]: range(0, 5)

    In [18]: list(range(5))
    Out[18]: [0, 1, 2, 3, 4]

`Методы словарей <../04_data_structures/6a_dict_methods.html>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Несколько изменений произошло в методах словарей.

dict.keys(), values(), items()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Методы keys(), values(), items() в Python 3 возвращают "views" вместо
списков. Особенность view заключается в том, что они меняются вместе с
изменением словаря. И фактически они лишь дают способ посмотреть на
соответствующие объекты, но не создают их копию.

В Python 3 нет методов:

* viewitems, viewkeys, viewvalues
* iteritems, iterkeys, itervalues

Для сравнения, методы словаря в Python 2.7:

.. code:: python

    In [19]: d = {1:100, 2:200, 3:300}

    In [20]: d.
        d.clear      d.get        d.iteritems  d.keys       d.setdefault d.viewitems
        d.copy       d.has_key    d.iterkeys   d.pop        d.update     d.viewkeys
        d.fromkeys   d.items      d.itervalues d.popitem    d.values     d.viewvalues

И в Python 3:

.. code:: python

    In [21]: d = {1:100, 2:200, 3:300}

    In [22]: d.
               clear()      get()        pop()        update()
               copy()       items()      popitem()    values()
               fromkeys()   keys()       setdefault()

`Распаковка переменных <../08_python_basic_examples/variable_unpacking.html>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

В Python 3 появилась возможность использовать ``*`` при распаковке
переменных:

.. code:: python

    In [23]: a, *b, c = [1,2,3,4,5]

    In [24]: a
    Out[24]: 1

    In [25]: b
    Out[25]: [2, 3, 4]

    In [26]: c
    Out[26]: 5

В Python 2.7 этот синтаксис не поддерживается:

.. code:: python

    In [27]: a, *b, c = [1, 2, 3, 4, 5]
      File "<ipython-input-10-e3f57143ffb4>", line 1
        a, *b, c = [1, 2, 3, 4, 5]
           ^
    SyntaxError: invalid syntax

`Итератор вместо списка <../10_useful_functions/>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

В Python 2.7 map, filter и zip возвращали список:

.. code:: python

    In [28]: map(str, [1, 2, 3, 4, 5])
    Out[28]: ['1', '2', '3', '4', '5']

    In [29]: filter(lambda x: x > 3, [1, 2, 3, 4, 5])
    Out[29]: [4, 5]

    In [30]: zip([1, 2, 3], [100, 200, 300])
    Out[30]: [(1, 100), (2, 200), (3, 300)]

В Python 3 они возвращают итератор:

.. code:: python

    In [31]: map(str, [1, 2, 3, 4, 5])
    Out[31]: <map at 0xb4ee3fec>

    In [32]: filter(lambda x: x > 3, [1, 2, 3, 4, 5])
    Out[32]: <filter at 0xb448c68c>

    In [33]: zip([1, 2, 3], [100, 200, 300])
    Out[33]: <zip at 0xb4efc1ec>

`subprocess.run <../12_useful_modules/subprocess.html>`__
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

В версии Python 3.5 в модуле subprocess появилась новая функция - run.
Она предоставляет более удобный интерфейс для работы с модулем и
получения вывода команд.

Соответственно, вместо функций call и check\_output используется функция
run, но функции call и check\_output остались.

Jinja2
~~~~~~

В модуле Jinja2 больше не нужно использовать такой код, так как
кодировка по умолчанию и так utf-8:

.. code:: python

    import sys     
    reload(sys)       
    sys.setdefaultencoding('utf-8')

В самих шаблонах, как и в Python, изменились методы словарей. Тут,
аналогично, вместо iteritems надо использовать items.

Модули pexpect, telnetlib, paramiko
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Модули pexpect, telnetlib, paramiko отправляют и получают байты, поэтому
надо делать encode/decode соответственно.

В netmiko эта конвертация выполняется автоматически.

Мелочи
~~~~~~

-  Название модуля Queue сменилось на queue
-  С версии Python 3.6 объект csv.DictReader возвращает OrderedDict
   вместо обычного словаря.

Дополнительная информация
~~~~~~~~~~~~~~~~~~~~~~~~~

Ниже приведены ссылки на ресурсы с информацией об изменениях в Python 3.

Документация:

-  `What’s New In Python
   3.0 <https://docs.python.org/3.0/whatsnew/3.0.html>`__
-  `Should I use Python 2 or Python 3 for my development
   activity? <https://wiki.python.org/moin/Python2orPython3>`__

Статьи:

-  `The key differences between Python 2.7.x and Python 3.x with
   examples <http://sebastianraschka.com/Articles/2014_python_2_3_key_diff.html>`__
-  `Supporting Python 3: An in-depth
   guide <http://python3porting.com/>`__

