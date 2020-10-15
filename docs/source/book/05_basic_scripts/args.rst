Передача аргументов скрипту (argv)
----------------------------------

Очень часто скрипт решает какую-то общую задачу. Например, скрипт
обрабатывает как-то файл конфигурации. Конечно, в таком случае не
хочется каждый раз руками в скрипте править название файла.

Гораздо лучше будет передавать имя файла как аргумент скрипта и затем
использовать уже указанный файл.

Модуль sys позволяет работать с аргументами скрипта с помощью argv.

Пример access_template_argv.py:

.. code:: python

    from sys import argv

    interface = argv[1]
    vlan = argv[2]

    access_template = ['switchport mode access',
                       'switchport access vlan {}',
                       'switchport nonegotiate',
                       'spanning-tree portfast',
                       'spanning-tree bpduguard enable']

    print('interface {}'.format(interface))
    print('\n'.join(access_template).format(vlan))

Проверка работы скрипта:

::

    $ python access_template_argv.py Gi0/7 4
    interface Gi0/7
    switchport mode access
    switchport access vlan 4
    switchport nonegotiate
    spanning-tree portfast
    spanning-tree bpduguard enable

Аргументы, которые были переданы скрипту, подставляются как значения в
шаблон.

Тут надо пояснить несколько моментов:

* argv - это список
* все аргументы находятся в списке в виде строк
* argv содержит не только аргументы, которые передали скрипту, но и название самого скрипта

В данном случае в списке argv находятся такие элементы:

::

    ['access_template_argv.py', 'Gi0/7', '4']

Сначала идет имя самого скрипта, затем аргументы, в том же порядке.

