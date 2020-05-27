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
файле test_task_9_1.py.

Пример теста test_task_9_1.py:

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
из каталога с заданиями раздела. Например, в
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
    $ pytest test_task_9_1.py
    ========================= test session starts ==========================
    platform linux -- Python 3.7.3, pytest-4.6.2, py-1.5.2, pluggy-0.12.0
    rootdir: /home/vagrant/repos/pyneng-7/pyneng-online-may-aug-2019/exercises/09_functions
    collected 3 items

    test_task_9_1.py ...                                       [100%]
    ...


conftest.py
~~~~~~~~~~~

К тестами относится и файл conftest.py - это
специальный файл, в котором можно писать функции (а точнее фикстуры)
общие для раных тестов. Например, в этот файл вынесены функции, которые
подключаются по SSH/Telnet к оборудованию.

pytest.ini
~~~~~~~~~~

Это конфигурационный файл pytest. В нем можно настроить аргументы вызова
pytest. Например, если вы хотите всегда вызывать pytest с -vv, надо
написать в pytest.ini:

::

    [pytest]
    addopts = -vv

В подготовленном файле pytest.ini находится такая строка:

::

    addopts = -vv --no-hints

Это параметр, который нужен модулю pytest-clarity, он описывается ниже.

Полезные команды
~~~~~~~~~~~~~~~~

Запуск одного теста:

::

    $ pytest test_task_9_1.py

Запуск одного теста с более подробным выводом информации (показывает
diff между данными в тесте и тем, что получено из функции):

::

    $ pytest test_task_9_1.py -vv

Запуск всех тестов одного раздела:

::

    [~/repos/pyneng-7/pyneng-online-may-aug-2019/exercises/09_functions]
    vagrant: [master|✔]
    $ pytest
    ======================= test session starts ========================
    platform linux -- Python 3.6.3, pytest-4.6.2, py-1.5.2, pluggy-0.12.0
    rootdir: /home/vagrant/repos/pyneng-7/pyneng-online-may-aug-2019/exercises/09_functions
    collected 21 items

    test_task_9_1.py ..F                                   [ 14%]
    test_task_9_1a.py FFF                                  [ 28%]
    test_task_9_2.py FFF                                   [ 42%]
    test_task_9_2a.py FFF                                  [ 57%]
    test_task_9_3.py FFF                                   [ 71%]
    test_task_9_3a.py FFF                                  [ 85%]
    test_task_9_4.py FFF                                   [100%]

    ...

Запуск всех тестов одного раздела с отображением сообщений об ошибках в
одну строку:

::

    $ pytest --tb=line


pytest-clarity
--------------

Плагин pytest-clarity улучшает отображение отличий необходимого
результата с решением задания.

Установка:

::

    pip install pytest-clarity

Плагин pytest-clarity отрабатывает только в том случае, когда тест
вызывается с флагом ``-vv``. Также по умолчанию у него довольно объемный
вывод, поэтому лучше вызывать его с аргументом ``--no-hints`` (эта опция прописана в подготовленном репозитории в файле pytest.ini):

::

    $ pytest test_task_9_3.py -vv --no-hints
    ======================= test session starts ========================

    test_task_9_3.py::test_function_created PASSED               [ 33%]
    test_task_9_3.py::test_function_params PASSED                [ 66%]
    test_task_9_3.py::test_function_return_value FAILED          [100%]

    ======================== FAILURES ==================================
    __________ test_function_return_value ______________________________

    ...
            access, trunk = return_value
    >       assert (
                return_value == correct_return_value
            ), "Функция возвращает неправильное значение"
    E       AssertionError: Функция возвращает неправильное значение
    E       assert left == right failed.
    E         Showing unified diff (L=left, R=right):
    E
    E          L ({'FastEthernet0/0': '10',
    E          R ({'FastEthernet0/0': 10,
    E          L   'FastEthernet0/2': '20',
    E          R   'FastEthernet0/2': 20,
    E          L   'FastEthernet1/0': '20',
    E          R   'FastEthernet1/0': 20,
    E          L   'FastEthernet1/1': '30'},
    E          R   'FastEthernet1/1': 30},
    E            {'FastEthernet0/1': [100, 200],
    E             'FastEthernet0/3': [100, 300, 400, 500, 600],
    E             'FastEthernet1/2': [400, 500, 600]})

    test_task_9_3.py:59: AssertionError

Так как агументы ``-vv`` и ``--no-hints`` надо постоянно передавать,
можно записать их в pytest.ini:

::

    [pytest]
    addopts = -vv --no-hints

