Пространства имен. Области видимости
------------------------------------

Область видимости определяет где переменная доступна. Область видимость переменной
зависит от того, где переменная создана.

Чаще всего, речь будет о двух областях видимости:

* глобальной - переменные, которые определены вне функции
* локальной - переменные, которые определены внутри функции

При использовании имен переменных в программе, Python каждый раз ищет,
создает или изменяет эти имена в соответствующем пространстве имен.
Пространство имен, которое доступно в каждый момент, зависит от области,
в которой находится код.

Поиск переменных
~~~~~~~~~~~~~~~~~

При поиске переменных, Python использует правило LEGB. Например, если
внутри функции выполняется обращение к имени переменной, Python ищет переменную
в таком порядке по областям видимости (до первого совпадения):

L (local) - в локальной (внутри функции)
E (enclosing) - в локальной области объемлющих функций (это те функции, внутри которых находится наша функция)
G (global) - в глобальной (в скрипте)
B (built-in) - во встроенной (зарезервированные значения Python)


.. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/09_function_legb_enclosing.png
   :align: center
   :class: only-light

.. only:: html

    .. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/09_function_legb_enclosing_dark.png
       :align: center
       :class: only-dark

Локальные и глобальные переменные
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Локальные переменные:
  
* переменные, которые определены внутри функции
* эти переменные становятся недоступными после выхода из функции

Глобальные переменные:
  
* переменные, которые определены вне функции
* эти переменные 'глобальны' только в пределах модуля, чтобы они были доступны
  в другом модуле, их надо импортировать

.. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/09_function_local_global.png
   :align: center
   :class: only-light

.. only:: html

    .. image:: https://raw.githubusercontent.com/natenka/pyneng-book/master/images/09_function_local_global_dark.png
       :align: center
       :class: only-dark

Пример локальной intf_config:

.. code:: python

    In [1]: def configure_intf(intf_name, ip, mask):
       ...:     intf_config = f'interface {intf_name}\nip address {ip} {mask}'
       ...:     return intf_config
       ...:

    In [2]: intf_config
    ---------------------------------------------------------------------------
    NameError                                 Traceback (most recent call last)
    <ipython-input-2-5983e972fb1c> in <module>
    ----> 1 intf_config

    NameError: name 'intf_config' is not defined


Обратите внимание, что переменная intf_config недоступна за пределами функции.
Для того чтобы получить результат функции, надо вызвать функцию и присвоить результат в переменную:

.. code:: python

    In [3]: result = configure_intf('F0/0', '10.1.1.1', '255.255.255.0')

    In [4]: result
    Out[4]: 'interface F0/0\nip address 10.1.1.1 255.255.255.0'


