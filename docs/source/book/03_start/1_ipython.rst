Интерпретатор Python. IPython
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Интерпретатор позволяет получать моментальный отклик на выполненные
действия. Можно сказать, что интерпретатор работает как CLI (Command
Line Interface) сетевых устройств: каждая команда будет выполняться
сразу же после нажатия Enter. Однако есть исключение – более сложные
объекты (например циклы или функции) выполняются только после
двухкратного нажатия Enter.

В предыдущем разделе, для проверки установки
Python вызывался стандартный интерпретатор. Кроме него, есть и
усовершенствованный интерпретатор 
`IPython <http://ipython.readthedocs.io/en/stable/index.html>`__.
IPython позволяет намного больше, чем стандартный
интерпретатор, который вызывается по команде python. Несколько примеров
(возможности IPython намного шире):

-  автодополнение команд по Tab или подсказка, если вариантов команд
   несколько;
-  более структурированный и понятный вывод команд;
-  автоматические отступы в циклах и других объектах;
-  можно передвигаться по истории выполнения команд, или же посмотреть
   её "волшебной" командой %history.

Установить IPython можно с помощью pip (установка будет производиться в
виртуальном окружении, если оно настроено):

::

    pip install ipython

После этого, перейти в IPython можно следующим образом:

::

    $ ipython
    Python 3.7.3 (default, May 13 2019, 15:44:23)
    Type 'copyright', 'credits' or 'license' for more information
    IPython 7.5.0 -- An enhanced Interactive Python. Type '?' for help.

    In [1]:

Для выхода используется команда quit. Далее описывается, как будет
использоваться IPython.

Для знакомства с интерпретатором можно попробовать использовать его как
калькулятор:

.. code:: python

    In [1]: 1 + 2
    Out[1]: 3

    In [2]: 22*45
    Out[2]: 990

    In [3]: 2**3
    Out[3]: 8

В IPython ввод и вывод помечены:

-  In – входные данные пользователя
-  Out – результат, который возвращает команда (если он есть)
-  числа после In или Out – это порядковые номера выполненных команд в
   текущей сессии IPython

Пример вывода строки функцией print():

.. code:: python

    In [4]: print('Hello!')
    Hello!

Когда в интерпретаторе создаётся, например, цикл, то внутри цикла
приглашение меняется на многоточие. Для выполнения цикла и выхода из
этого подрежима необходимо дважды нажать Enter:

.. code:: python

    In [5]: for i in range(5):
       ...:     print(i)
       ...:     
    0
    1
    2
    3
    4

help()
^^^^^^

В IPython есть возможность посмотреть справку по произвольному объекту,
функции или методу с помощью help():

::

    In [1]: help(str)
    Help on class str in module builtins:
     
    class str(object)
     |  str(object='') -> str
     |  str(bytes_or_buffer[, encoding[, errors]]) -> str
     |
     |  Create a new string object from the given object. If encoding or
     |  errors is specified, then the object must expose a data buffer
     |  that will be decoded using the given encoding and error handler.
    ...
     
    In [2]: help(str.strip)
    Help on method_descriptor:
     
    strip(...)
        S.strip([chars]) -> str
     
        Return a copy of the string S with leading and trailing
        whitespace removed.
        If chars is given and not None, remove characters in chars instead.

Второй вариант:

::

    In [3]: ?str
    Init signature: str(self, /, *args, **kwargs)
    Docstring:
    str(object='') -> str
    str(bytes_or_buffer[, encoding[, errors]]) -> str
     
    Create a new string object from the given object. If encoding or
    errors is specified, then the object must expose a data buffer
    that will be decoded using the given encoding and error handler.
    Otherwise, returns the result of object.__str__() (if defined)
    or repr(object).
    encoding defaults to sys.getdefaultencoding().
    errors defaults to 'strict'.
    Type:           type
     
    In [4]: ?str.strip
    Docstring:
    S.strip([chars]) -> str
     
    Return a copy of the string S with leading and trailing
    whitespace removed.
    If chars is given and not None, remove characters in chars instead.
    Type:      method_descriptor

print()
^^^^^^^

Функция ``print()`` позволяет вывести информацию на стандартный поток вывода
(текущий экран терминала). Если необходимо вывести строку, то её нужно
обязательно заключить в кавычки (двойные или одинарные). Если же нужно
вывести, например, результат вычисления или просто число, то кавычки не
нужны:

.. code:: python

    In [6]: print('Hello!')
    Hello!

    In [7]: print(5*5)
    25

Если нужно вывести подряд несколько значений через пробел, то нужно
перечислить их через запятую:

.. code:: python

    In [8]: print(1*5, 2*5, 3*5, 4*5)
    5 10 15 20

    In [9]: print('one', 'two', 'three')
    one two three

По умолчанию в конце каждого выражения, переданного в print(), будет
перевод строки. Если необходимо, чтобы после вывода каждого выражения не
было бы перевода строки, надо в качестве последнего выражения в print()
указать дополнительный аргумент end.

.. seealso:: Дополнительные параметры функции print :ref:`print`

dir()
^^^^^

Функция ``dir()`` может использоваться для того, чтобы посмотреть, какие имеются
атрибуты (переменные, привязанные к объекту) и методы (функции,
привязанные к объекту).

Например, для числа вывод будет таким (обратите внимание на различные
методы, которые позволяют делать арифметические операции):

.. code:: python

    In [10]: dir(5)
    Out[10]: 
    ['__abs__',
     '__add__',
     '__and__',
     ...
     'bit_length',
     'conjugate',
     'denominator',
     'imag',
     'numerator',
     'real']

Аналогично для строки:

.. code:: python

    In [11]: dir('hello')
    Out[11]: 
    ['__add__',
     '__class__',
     '__contains__',
     ...
     'startswith',
     'strip',
     'swapcase',
     'title',
     'translate',
     'upper',
     'zfill']

Если выполнить dir() без передачи значения, то она показывает
существующие методы, атрибуты и переменные, определённые в текущей
сессии интерпретатора:

.. code:: python

    In [12]: dir()
    Out[12]: 
    [ '__builtin__',
     '__builtins__',
     '__doc__',
     '__name__',
     '_dh',
     ...
     '_oh',
     '_sh',
     'exit',
     'get_ipython',
     'i',
     'quit']

Например, после создания переменной a и test():

.. code:: python

    In [13]: a = 'hello'

    In [14]: def test():
       ....:     print('test')
       ....:     

    In [15]: dir()
    Out[15]: 
     ...
     'a',
     'exit',
     'get_ipython',
     'i',
     'quit',
     'test']

