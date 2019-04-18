Создание базовых скриптов
=========================

Если говорить в целом, то скрипт - это обычный файл. В этом файле
хранится последовательность команд, которые необходимо выполнить.

Начнем с базового скрипта. Выведем на стандартный поток вывода несколько
строк.

Для этого надо создать файл access\_template.py с таким содержимым:

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

    Ставить расширение .py у файла не обязательно.

    Но, если Вы используете Windows, то это желательно делать, так как
    Windows использует расширение файла для определения того, как
    обрабатывать файл.

    В курсе все скрипты, которые будут создаваться, используют
    расширение .py. Можно сказать, что это "хороший тон" - создавать
    скрипты Python с таким расширением.

Исполняемый файл
~~~~~~~~~~~~~~~~

Для того, чтобы файл был исполняемым, и не нужно было каждый раз писать
python перед вызовом файла, нужно: \* сделать файл исполняемым (для
linux) \* в первой строке файла должна находиться строка
``#!/usr/bin/env python`` или ``#!/usr/bin/env python3``, в зависимости
от того, какая версия Python используется по умолчанию

Пример файла access\_template\_exec.py:

.. code:: python

    #!/usr/bin/env python3

    access_template = ['switchport mode access',
                       'switchport access vlan {}',
                       'switchport nonegotiate',
                       'spanning-tree portfast',
                       'spanning-tree bpduguard enable']

    print('\n'.join(access_template).format(5))

После этого:

::

    chmod +x access_template_exec.py

Теперь можно вызывать файл таким образом:

::

    $ ./access_template_exec.py

