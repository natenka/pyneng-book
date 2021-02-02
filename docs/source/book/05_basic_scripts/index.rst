.. raw:: latex

   \newpage

.. _basic_scripts_index:

5. Создание базовых скриптов
============================

Если говорить в целом, то скрипт - это обычный файл. В этом файле
хранится последовательность команд, которые необходимо выполнить.

Начнем с базового скрипта. Выведем на стандартный поток вывода несколько
строк.

Для этого надо создать файл access_template.py с таким содержимым:

.. code:: python

    access_template = ['switchport mode access',
                       'switchport access vlan {}',
                       'switchport nonegotiate',
                       'spanning-tree portfast',
                       'spanning-tree bpduguard enable']

    print('\n'.join(access_template).format(5))

Сначала элементы списка объединяются в строку, которая разделена
символом ``\n``, а в строку подставляется номер VLAN, используя
форматирование строк.

После этого надо сохранить файл и перейти в командную строку.

Так выглядит выполнение скрипта:

.. code:: python

    $ python access_template.py
    switchport mode access
    switchport access vlan 5
    switchport nonegotiate
    spanning-tree portfast
    spanning-tree bpduguard enable

Ставить расширение ``.py`` у файла не обязательно, но, если вы используете Windows,
то это желательно делать, так как Windows использует расширение файла для определения того, как
обрабатывать файл.

В курсе все скрипты, которые будут создаваться, используют
расширение .py. Можно сказать, что это "хороший тон" - создавать
скрипты Python с таким расширением.


.. toctree::
   :maxdepth: 1

   executable
   args
   user_input
   ../../exercises/05_exercises
