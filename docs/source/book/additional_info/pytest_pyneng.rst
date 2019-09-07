Особенности использования pytest для проверки заданий
-----------------------------------------------------

На курсе pytest используется, в первую очередь, для самопроверки
заданий. Однако, эта проверка не является опциональной - задание
считается сделанным, когда оно соблюдает все указанные пункты и проходит
тесты. Со своей стороны, я тоже сначала проверяю задания автоматическими
тестами, а затем уже смотрю код, пишу комментарии, если нужно и
показываю вариант решения.

Поначалу тесты требуют усилий, но через пару разделов, они будут
помогать в решении заданий.

.. warning::

    Тесты, которые написаны для заданий курса, не
    являются эталоном или best practice написания тестов. Тесты написаны
    с максимальным упором на понятность и многие вещи принято делать
    по-другому.

При решении заданий, особенно, когда есть сомнения по поводу итогового
формата данных, которые должны быть получены, лучше посмтреть в тест.
Например, если задание task_9_1.py, то соответствующий тест будет в
файле tests/test_task_9_1.py.

Пример теста tests/test_task_9_1.py:

.. code:: python

    import pytest
    import task_9_1
    import sys
    sys.path.append('..')

    from common_functions import check_function_exists, check_function_params


    # Проверяет создана ли функция generate_access_config в задании task_9_1
    def test_function_created():
        check_function_exists(task_9_1, 'generate_access_config')

    # Проверяет параметры функции
    def test_function_params():
        check_function_params(function=task_9_1.generate_access_config,
                              param_count=2, param_names=['intf_vlan_mapping', 'access_template'])


    def test_function_return_value():
        access_vlans_mapping = {
            'FastEthernet0/12': 10,
            'FastEthernet0/14': 11,
            'FastEthernet0/16': 17
        }
        template_access_mode = [
            'switchport mode access', 'switchport access vlan',
            'switchport nonegotiate', 'spanning-tree portfast',
            'spanning-tree bpduguard enable'
        ]
        correct_return_value = ['interface FastEthernet0/12',
                                'switchport mode access',
                                'switchport access vlan 10',
                                'switchport nonegotiate',
                                'spanning-tree portfast',
                                'spanning-tree bpduguard enable',
                                'interface FastEthernet0/14',
                                'switchport mode access',
                                'switchport access vlan 11',
                                'switchport nonegotiate',
                                'spanning-tree portfast',
                                'spanning-tree bpduguard enable',
                                'interface FastEthernet0/16',
                                'switchport mode access',
                                'switchport access vlan 17',
                                'switchport nonegotiate',
                                'spanning-tree portfast',
                                'spanning-tree bpduguard enable']

        return_value = task_9_1.generate_access_config(access_vlans_mapping, template_access_mode)
        assert return_value != None, "Функция ничего не возвращает"
        assert type(return_value) == list, "Функция должна возвращать список"
        assert return_value == correct_return_value, "Функция возвращает неправильное значение"

Обратите внимание на переменную correct_return_value - в этой
переменной содержится итоговый список, который должна возвращать функция
generate_access_config. Поэтому, если, к примеру, по мере выполнения
задания, возник вопрос надо ли добавлять пробелы перед командами или
перевод строки в конце, можно посмотреть в тесте, что именно требуется в
результате. А также сверить свой вывод, с выводом в переменной
correct_return_value.

Как запускать тесты для проверки заданий
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Самое главное, это откуда надо запускать тесты: все тесты надо запускать
из каталога с заданиями раздела, а не из каталога tests. Например, в
разделе 09_functions, такая структура каталога с заданиями:

::

    [~/repos/pyneng-7/pyneng-online-may-aug-2019/exercises/09_functions]
    vagrant: [master|✔]
    $ tree
    .

    ├── config_r1.txt
    ├── config_sw1.txt
    ├── config_sw2.txt
    ├── conftest.py
    ├── task_9_1a.py
    ├── task_9_1.py
    ├── task_9_2a.py
    ├── task_9_2.py
    ├── task_9_3a.py
    ├── task_9_3.py
    ├── task_9_4.py
    └── tests
        ├── test_task_9_1a.py
        ├── test_task_9_1.py
        ├── test_task_9_2a.py
        ├── test_task_9_2.py
        ├── test_task_9_3a.py
        ├── test_task_9_3.py
        └── test_task_9_4.py

Запускать тесты, в этом случае, надо из каталога 09_functions:

::

    [~/repos/pyneng-7/pyneng-online-may-aug-2019/exercises/09_functions]
    vagrant: [master|✔]
    $ pytest tests/test_task_9_1.py
    ========================= test session starts ==========================
    platform linux -- Python 3.7.3, pytest-4.6.2, py-1.5.2, pluggy-0.12.0
    rootdir: /home/vagrant/repos/pyneng-7/pyneng-online-may-aug-2019/exercises/09_functions
    collected 3 items

    tests/test_task_9_1.py ...                                       [100%]
    ...

    При запуске тестов из каталога tests, возникнут ошибки и тесты не
    будут выполняться.

conftest.py
~~~~~~~~~~~

Кроме каталога test, к тестами относится и файл conftest.py - это
специальный файл, в котором можно писать функции (а точнее фикстуры)
общие для раных тестов. Например, в этот файл вынесены функции, которые
подключаются по SSH/Telnet к оборудованию.

Полезные команды
~~~~~~~~~~~~~~~~

Запуск одного теста:

::

    $ pytest tests/test_task_9_1.py

Запуск одного теста с более подробным выводом информации (показывает
diff между данными в тесте и тем, что получено из функции):

::

    $ pytest tests/test_task_9_1.py -vv

Запуск всех тестов одного раздела:

::

    [~/repos/pyneng-7/pyneng-online-may-aug-2019/exercises/09_functions]
    vagrant: [master|✔]
    $ pytest
    ======================= test session starts ========================
    platform linux -- Python 3.6.3, pytest-4.6.2, py-1.5.2, pluggy-0.12.0
    rootdir: /home/vagrant/repos/pyneng-7/pyneng-online-may-aug-2019/exercises/09_functions
    collected 21 items

    tests/test_task_9_1.py ..F                                   [ 14%]
    tests/test_task_9_1a.py FFF                                  [ 28%]
    tests/test_task_9_2.py FFF                                   [ 42%]
    tests/test_task_9_2a.py FFF                                  [ 57%]
    tests/test_task_9_3.py FFF                                   [ 71%]
    tests/test_task_9_3a.py FFF                                  [ 85%]
    tests/test_task_9_4.py FFF                                   [100%]

    ...

Запуск всех тестов одного раздела с отображением сообщений об ошибках в
одну строку:

::

    $ pytest --tb=line

