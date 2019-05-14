Специальные команды ipython
^^^^^^^^^^^^^^^^^^^^^^^^^^^

В IPython есть специальные команды, которые упрощают работу
с интерпретатором. Все они начинаются со знака процента.

%history
''''''''

Например, команда ``%history`` позволяет просмотреть историю введённых пользователем
команд в текущей сессии IPython:

.. code:: python

    In [1]: a = 10

    In [2]: b = 5

    In [3]: if a > b:
       ...:     print("A is bigger")
       ...:
    A is bigger

    In [4]: %history
    a = 10
    b = 5
    if a > b:
        print("A is bigger")
    %history

С помощью %history можно скопировать нужный блок кода.

%cpaste
'''''''

Ещё одна очень полезная "волшебная" команда это %cpaste.

При вставке кода с отступами в IPython, из-за автоматических отступов
самого IPython, код начинает дополнительно сдвигаться:

.. code:: python

    In [1]: a = 10

    In [2]: b = 5

    In [3]: if a > b:
       ...:     print("A is bigger")
       ...: else:
       ...:     print("A is less or equal")
       ...:
    A is bigger

    In [4]: %hist
    a = 10
    b = 5
    if a > b:
        print("A is bigger")
    else:
        print("A is less or equal")
    %hist

    In [5]: if a > b:
       ...:         print("A is bigger")
       ...:     else:
       ...:             print("A is less or equal")
       ...:
      File "<ipython-input-8-4d18ff094f5c>", line 3
        else:
             ^
    IndentationError: unindent does not match any outer indentation level
    If you want to paste code into IPython, try the %paste and %cpaste magic functions.

Обратите внимание на последнюю строку – IPython подсказывает, какой
командой воспользоваться, чтобы корректно вставить такой код. Команды
%paste и %cpaste работают немного по-разному.

При использовании %cpaste, после того, как все строки скопированы, надо
завершить работу команды, набрав "--":

.. code:: python

    In [9]: %cpaste
    Pasting code; enter '--' alone on the line to stop or use Ctrl-D.
    :if a > b:
    :    print("A is bigger")
    :else:
    :    print("A is less or equal")
    :--
    A is bigger

%paste (требует установленного Tkinter):

.. code:: python

    In [10]: %paste
    if a > b:
        print("A is bigger")
    else:
        print("A is less or equal")

    ## -- End pasted text --
    A is bigger

Подробнее об IPython можно почитать в
`документации <http://ipython.readthedocs.io/en/stable/index.html>`__
IPython.

Коротко информацию можно посмотреть в самом IPython командой %quickref:

::

    IPython -- An enhanced Interactive Python - Quick Reference Card
    ===========================================================

    obj?, obj??      : Get help, or more help for object (also works as
                       ?obj, ??obj).
    ?foo.*abc*       : List names in 'foo' containing 'abc' in them.
    %magic           : Information about IPython's 'magic' % functions.

    Magic functions are prefixed by % or %%, and typically take their arguments
    without parentheses, quotes or even commas for convenience.  Line magics take a
    single % and cell magics are prefixed with two %%.

    Example magic function calls:

    %alias d ls -F   : 'd' is now an alias for 'ls -F'
    alias d ls -F    : Works if 'alias' not a python name
    alist = %alias   : Get list of aliases to 'alist'
    cd /usr/share    : Obvious. cd -<tab> to choose from visited dirs.
    %cd??            : See help AND source for magic %cd
    %timeit x=10     : time the 'x=10' statement with high precision.
    %%timeit x=2**100
    x**100           : time 'x**100' with a setup of 'x=2**100'; setup code is not
                       counted.  This is an example of a cell magic.

    System commands:

    !cp a.txt b/     : System command escape, calls os.system()
    cp a.txt b/      : after %rehashx, most system commands work without !
    cp ${f}.txt $bar : Variable expansion in magics and system commands
    files = !ls /usr : Capture sytem command output
    files.s, files.l, files.n: "a b c", ['a','b','c'], 'a\nb\nc'

    History:

    _i, _ii, _iii    : Previous, next previous, next next previous input
    _i4, _ih[2:5]    : Input history line 4, lines 2-4
    exec _i81        : Execute input history line #81 again
    %rep 81          : Edit input history line #81
    _, __, ___       : previous, next previous, next next previous output
    _dh              : Directory history
    _oh              : Output history
    %hist            : Command history of current session.
    %hist -g foo     : Search command history of (almost) all sessions for 'foo'.
    %hist -g         : Command history of (almost) all sessions.
    %hist 1/2-8      : Command history containing lines 2-8 of session 1.
    %hist 1/ ~2/     : Command history of session 1 and 2 sessions before current.

