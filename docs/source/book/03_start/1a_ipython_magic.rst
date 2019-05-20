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


%time
'''''

Команда ``%time`` показывает сколько секунд выполнялось выражение:

.. code:: python

    In [5]: import subprocess

    In [6]: def ping_ip(ip_address):
        ..:     reply = subprocess.run(['ping', '-c', '3', '-n', ip_address],
        ..:                            stdout=subprocess.PIPE,
        ..:                            stderr=subprocess.PIPE,
        ..:                            encoding='utf-8')
        ..:     if reply.returncode == 0:
        ..:         return True
        ..:     else:
        ..:         return False
        ..:

    In [7]: %time ping_ip('8.8.8.8')
    CPU times: user 0 ns, sys: 4 ms, total: 4 ms
    Wall time: 2.03 s
    Out[7]: True

    In [8]: %time ping_ip('8.8.8')
    CPU times: user 0 ns, sys: 8 ms, total: 8 ms
    Wall time: 12 s
    Out[8]: False

    In [9]: items = [1, 3, 5, 7, 9, 1, 2, 3, 55, 77, 33]

    In [10]: %time sorted(items)
    CPU times: user 0 ns, sys: 0 ns, total: 0 ns
    Wall time: 8.11 µs
    Out[10]: [1, 1, 2, 3, 3, 5, 7, 9, 33, 55, 77]



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

