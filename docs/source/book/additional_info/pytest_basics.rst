Основы pytest
-------------

Для начала надо установить pytest и pyyaml:

::

    pip install pytest
    pip install pyyaml

Хотя на курсе не надо будет писать тесты, чтобы их понимать, стоит
посмотреть на пример теста. Например, есть следующий код с функцией
check_ip:

.. code:: python

    import ipaddress


    def check_ip(ip):
        try:
            ipaddress.ip_address(ip)
            return True
        except ValueError as err:
            return False


    if __name__ == "__main__":
        result = check_ip('10.1.1.1')
        print('Function result:', result)

Функция check_ip проверяет является ли аргумент, который ей передали,
IP-адресом. Пример вызова функции с разными аргументами:

.. code:: python

    In [1]: import ipaddress
       ...:
       ...:
       ...: def check_ip(ip):
       ...:     try:
       ...:         ipaddress.ip_address(ip)
       ...:         return True
       ...:     except ValueError as err:
       ...:         return False
       ...:

    In [2]: check_ip('10.1.1.1')
    Out[2]: True

    In [3]: check_ip('10.1.')
    Out[3]: False

    In [4]: check_ip('a.a.a.a')
    Out[4]: False

    In [5]: check_ip('500.1.1.1')
    Out[5]: False

Теперь необходимо написать тест для функции check_ip. Тест должен
проверять, что при передаче корректного адреса, функция возвращает True,
а при передаче неправильного аргумента - False.

Чтобы упростиь задачу, тест можно написать в том же файле. В pytest,
тестом может быть обычная функция, с именем, которое начинается на
test_. Внутри функции надо написать условия, которые проверяются. В
pytest это делается с помощью assert.

assert
~~~~~~

assert ничего не делает, если выражение, которое написано после него
истинное и генерирует исключение, если выражение ложное:

.. code:: python

    In [6]: assert 5 > 1

    In [7]: a = 4

    In [8]: assert a in [1,2,3,4]

    In [9]: assert a not in [1,2,3,4]
    ---------------------------------------------------------------------------
    AssertionError                            Traceback (most recent call last)
    <ipython-input-9-1956288e2d8e> in <module>
    ----> 1 assert a not in [1,2,3,4]

    AssertionError:

    In [10]: assert 5 < 1
    ---------------------------------------------------------------------------
    AssertionError                            Traceback (most recent call last)
    <ipython-input-10-b224d03aab2f> in <module>
    ----> 1 assert 5 < 1

    AssertionError:

После assert и выражения можно писать сообщение. Если сообщение есть,
оно выводится в исключении:

.. code:: python

    In [11]: assert a not in [1,2,3,4], "а нет в списке"
    ---------------------------------------------------------------------------
    AssertionError                            Traceback (most recent call last)
    <ipython-input-11-7a8f87272a54> in <module>
    ----> 1 assert a not in [1,2,3,4], "а нет в списке"

    AssertionError: а нет в списке

Пример теста
~~~~~~~~~~~~

pytest использует assert, чтобы указать какие условия должны
выполняться, чтобы тест считался пройденным.

В pytest тест можно написать как обычную функцию, но имя функции должно
начинаться с test_. Ниже написан тест test_check_ip, который
проверяет работу функции check_ip, передав ей два значения: правильный
адрес и неправильный, а также после каждой проверки написано сообщение:

.. code:: python

    import ipaddress


    def check_ip(ip):
        try:
            ipaddress.ip_address(ip)
            return True
        except ValueError as err:
            return False


    def test_check_ip():
        assert check_ip('10.1.1.1') == True, 'При правильном IP, функция должна возвращать True'
        assert check_ip('500.1.1.1') == False, 'Если адрес неправильный, функция должна возвращать False'


    if __name__ == "__main__":
        result = check_ip('10.1.1.1')
        print('Function result:', result)

Код записан в файл check_ip_functions.py. Теперь надо разобраться как
вызывать тесты. Самый простой вариант, написать слово pytest. В этом
случае, pytest автоматически обнаружит тесты в текущем каталоге. Однако,
у pytest есть определенные правила, не только по названию функцию, но и
по названию файлов с тестами - имена файлов также должны начинаться на
test_. Если правила соблюдаются, pytest автоматически найдет тесты,
если нет - надо указать файл с тестами.

В случае с примером выше, надо будет вызвать такую команду:

::

    $ pytest check_ip_functions.py
    ========================= test session starts ==========================
    platform linux -- Python 3.7.3, pytest-4.6.2, py-1.5.2, pluggy-0.12.0
    rootdir: /home/vagrant/repos/general/pyneng.github.io/code_examples/pytest
    collected 1 item

    check_ip_functions.py .                                          [100%]

    ======================= 1 passed in 0.02 seconds =======================

По умолчанию, если тесты проходят, каждый тест (функция test_check_ip)
отмечается точкой. Так как в данном случае тест только один - функция
test_check_ip, после имени check_ip_functions.py стоит точка, а
также ниже написано, что 1 тест прошел.

Теперь, допустим, что функция работает неправильно и всегда возвращает
False (напишите return False в самом начале функции). В этом случае,
выполнение теста будет выглядеть так:

::

    $ pytest check_ip_functions.py
    ========================= test session starts ==========================
    platform linux -- Python 3.6.3, pytest-4.6.2, py-1.5.2, pluggy-0.12.0
    rootdir: /home/vagrant/repos/general/pyneng.github.io/code_examples/pytest
    collected 1 item

    check_ip_functions.py F                                          [100%]

    =============================== FAILURES ===============================
    ____________________________ test_check_ip _____________________________

        def test_check_ip():
    >       assert check_ip('10.1.1.1') == True, 'При правильном IP, функция должна возвращать True'
    E       AssertionError: При правильном IP, функция должна возвращать True
    E       assert False == True
    E        +  where False = check_ip('10.1.1.1')

    check_ip_functions.py:14: AssertionError
    ======================= 1 failed in 0.06 seconds =======================

Если тест не проходит, pytest выводит более подробную информацию и
показывает в каком месте что-то пошло не так. В данном случае, при
выполении строки ``assert check_ip('10.1.1.1') == True``, выражение не
дало истинный результат, поэтому было сгенерировано исключение.

Ниже, pytest показывает, что именно он сравнивал:
``assert False == True`` и уточняет, что False - это
``check_ip('10.1.1.1')``. Посмотрев на вывод, можно заподозрить, что с
функцией check_ip что-то не так, так как она возвращает False на
правильном адресе.

Чаще всего, тесты пишутся в отдельных файлах. Для данного примера тест
всего один, но он все равно вынесен в отдельный файл.

Файл test_check_ip_function.py:

.. code:: python

    from check_ip_functions import check_ip


    def test_check_ip():
        assert check_ip('10.1.1.1') == True, 'При правильном IP, функция должна возвращать True'
        assert check_ip('500.1.1.1') == False, 'Если адрес неправильный, функция должна возвращать False'

Файл check_ip_functions.py:

.. code:: python

    import ipaddress


    def check_ip(ip):
        #return False
        try:
            ipaddress.ip_address(ip)
            return True
        except ValueError as err:
            return False


    if __name__ == "__main__":
        result = check_ip('10.1.1.1')
        print('Function result:', result)

В таком случае, тест можно запустить не указывая файл:

::

    $ pytest
    ================= test session starts ========================
    platform linux -- Python 3.6.3, pytest-4.6.2, py-1.5.2, pluggy-0.12.0
    rootdir: /home/vagrant/repos/general/pyneng.github.io/code_examples/pytest
    collected 1 item

    test_check_ip_function.py .                              [100%]

    ================= 1 passed in 0.02 seconds ====================

