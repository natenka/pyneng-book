Задания
=======

.. include:: ./exercises_intro.rst

Задание 11.1
~~~~~~~~~~~~

Создать функцию parse\_cdp\_neighbors, которая обрабатывает вывод
команды show cdp neighbors.

Функция ожидает, как аргумент, вывод команды одной строкой (а не имя
файла).

Функция должна возвращать словарь, который описывает соединения между
устройствами.

Например, если как аргумент был передан такой вывод:

::

    R4>show cdp neighbors

    Device ID    Local Intrfce   Holdtme     Capability       Platform    Port ID
    R5           Fa 0/1          122           R S I           2811       Fa 0/1
    R6           Fa 0/2          143           R S I           2811       Fa 0/0

Функция должна вернуть такой словарь:

.. code:: python

    {('R4', 'Fa0/1'): ('R5', 'Fa0/1'),
     ('R4', 'Fa0/2'): ('R6', 'Fa0/0')}

Интерфейсы могут быть записаны с пробелом Fa 0/0 или без Fa0/0.

Проверить работу функции на содержимом файла sw1\_sh\_cdp\_neighbors.txt

Ограничение: Все задания надо выполнять используя только пройденные
темы.

Задание 11.2
~~~~~~~~~~~~

    Для выполнения этого задания, должен быть установлен graphviz:

    ``apt-get install graphviz``

    И модуль python для работы с graphviz:

    ``pip install graphviz``

С помощью функции parse\_cdp\_neighbors из задания 11.1 и функции
draw\_topology из файла draw\_network\_graph.py, сгенерировать
топологию, которая соответствует выводу команды sh cdp neighbor в файле
sw1\_sh\_cdp\_neighbors.txt

Не копировать код функций parse\_cdp\_neighbors и draw\_topology.

В итоге, должен быть сгенерировано изображение топологии. Результат
должен выглядеть так же, как схема в файле task\_11\_2\_topology.svg

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/08_modules/task_8_2a_topology.png
   :alt: task\_8\_2a\_topology

   task\_8\_2a\_topology
При этом: \* Интерфейсы могут быть записаны с пробелом Fa 0/0 или без
Fa0/0. \* Расположение устройств на схеме может быть другим \*
Соединения должны соответствовать схеме

Ограничение: Все задания надо выполнять используя только пройденные
темы.

Задание 11.2a
~~~~~~~~~~~~~

    Для выполнения этого задания, должен быть установлен graphviz:

    ``apt-get install graphviz``

    И модуль python для работы с graphviz:

    ``pip install graphviz``

С помощью функции parse\_cdp\_neighbors из задания 11.1 и функции
draw\_topology из файла draw\_network\_graph.py, сгенерировать
топологию, которая соответствует выводу команды sh cdp neighbor из
файлов: \* sh\_cdp\_n\_sw1.txt \* sh\_cdp\_n\_r1.txt \*
sh\_cdp\_n\_r2.txt \* sh\_cdp\_n\_r3.txt

Не копировать код функций parse\_cdp\_neighbors и draw\_topology.

В итоге, должен быть сгенерировано изображение топологии. Результат
должен выглядеть так же, как схема в файле task\_11\_2a\_topology.svg

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/08_modules/task_8_2b_topology.png
   :alt: task\_8\_2b\_topology

   task\_8\_2b\_topology
При этом: \* Интерфейсы могут быть записаны с пробелом Fa 0/0 или без
Fa0/0. \* Расположение устройств на схеме может быть другим \*
Соединения должны соответствовать схеме

Ограничение: Все задания надо выполнять используя только пройденные
темы.
