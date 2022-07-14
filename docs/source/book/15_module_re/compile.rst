Функция compile
---------------

В Python есть возможность заранее скомпилировать регулярное выражение, а
затем использовать его. Это особенно полезно в тех случаях, когда
регулярное выражение много используется в скрипте.

Использование компилированного выражения может ускорить обработку, и,
как правило, такой вариант удобней использовать, так как в программе
разделяется создание регулярного выражения и его использование. Кроме
того, при использовании функции ``re.compile`` создается объект ``RegexObject``,
у которого есть несколько дополнительных возможностей, которых нет в
объекте ``MatchObject``.

Для компиляции регулярного выражения используется функция ``re.compile``:

.. code:: python

    In [52]: regex = re.compile(r'\d+ +\S+ +\w+ +\S+')


У объекта который возвращает re.compile, доступны методы search, match,
finditer, findall. Это те же функции, которые доступны в модуле
глобально, но теперь их надо применять к объекту.

Пример использования метода search:

.. code:: python

    In [67]: line = ' 100    a1b2.ac10.7000    DYNAMIC     Gi0/1'

    In [68]: match = regex.search(line)

Теперь search надо вызывать как метод объекта regex. И передать как
аргумент строку.

Результатом будет объект Match:

.. code:: python

    In [69]: match
    Out[69]: <_sre.SRE_Match object; span=(1, 43), match='100    a1b2.ac10.7000    DYNAMIC     Gi0/1'>

    In [70]: match.group()
    Out[70]: '100    a1b2.ac10.7000    DYNAMIC     Gi0/1'

При использовании re.compile, флаги надо указывать внутри re.compile, не в
методах search/finditer:

.. code:: python

    regex = re.compile(r'^Device ID: \S+.+?Cisco IOS', re.MULTILINE | re.DOTALL)
    match_all = regex.finditer(output)


Пример компиляции регулярного выражения и его использования на примере
разбора лог-файла (файл parse_log_compile.py):

.. code:: python

    import re

    regex = re.compile(r'Host \S+ '
                       r'in vlan (\d+) '
                       r'is flapping between port '
                       r'(\S+) and port (\S+)')

    ports = set()

    with open('log.txt') as f:
        for m in regex.finditer(f.read()):
            vlan = m.group(1)
            ports.add(m.group(2))
            ports.add(m.group(3))

    print('Петля между портами {} в VLAN {}'.format(', '.join(ports), vlan))

Это модифицированный пример с использованием finditer. Тут изменилось
описание регулярного выражения:

.. code:: python

    regex = re.compile(r'Host \S+ '
                       r'in vlan (\d+) '
                       r'is flapping between port '
                       r'(\S+) and port (\S+)')

И вызов finditer теперь выполняется как метод объекта regex:

.. code:: python

        for m in regex.finditer(f.read()):

Параметры, которые доступны только при использовании re.compile
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

При использовании функции re.compile в методах search, match, findall,
finditer и fullmatch появляются дополнительные параметры: 

* pos - позволяет указывать индекс в строке, с которого надо начать искать совпадение 
* endpos - указывает, до какого индекса надо выполнять поиск

Их использование аналогично выполнению среза строки.

Например, таким будет результат без указания параметров pos, endpos:

.. code:: python

    In [75]: regex = re.compile(r'\d+ +\S+ +\w+ +\S+')

    In [76]: line = ' 100    a1b2.ac10.7000    DYNAMIC     Gi0/1'

    In [77]: match = regex.search(line)

    In [78]: match.group()
    Out[78]: '100    a1b2.ac10.7000    DYNAMIC     Gi0/1'

В этом случае указывается начальная позиция поиска:

.. code:: python

    In [79]: match = regex.search(line, 2)

    In [80]: match.group()
    Out[80]: '00    a1b2.ac10.7000    DYNAMIC     Gi0/1'

Указание начальной позиции аналогично срезу строки:

.. code:: python

    In [81]: match = regex.search(line[2:])

    In [82]: match.group()
    Out[82]: '00    a1b2.ac10.7000    DYNAMIC     Gi0/1'

И последний пример - использование двух индексов:

.. code:: python

    In [90]: line = ' 100    a1b2.ac10.7000    DYNAMIC     Gi0/1'

    In [91]: regex = re.compile(r'\d+ +\S+ +\w+ +\S+')

    In [92]: match = regex.search(line, 2, 40)

    In [93]: match.group()
    Out[93]: '00    a1b2.ac10.7000    DYNAMIC     Gi'

И аналогичный срез строки:

.. code:: python

    In [94]: match = regex.search(line[2:40])

    In [95]: match.group()
    Out[95]: '00    a1b2.ac10.7000    DYNAMIC     Gi'

В методах match, findall, finditer и fullmatch параметры pos и endpos
работают аналогично.
