Параметр self
~~~~~~~~~~~~~

Параметр self указывался выше в определении методов, а также при
использовании переменных экземпляра в методе. Параметр self это ссылка
на конкретный экземпляр класса. При этом, само имя self не является
особенным, а лишь договоренностью. Вместо self можно использовать другое
имя, но так делать не стоит.

Пример с использованием другого имени, вместо self:

.. code:: python

    In [15]: class Switch:
        ...:     def info(sw_object):
        ...:         print(f'Hostname: {sw_object.hostname}\nModel: {sw_object.model}')
        ...:

Работать все будет аналогично:

.. code:: python

    In [16]: sw1 = Switch()

    In [17]: sw1.hostname = 'sw1'

    In [18]: sw1.model = 'Cisco 3850'

    In [19]: sw1.info()
    Hostname: sw1
    Model: Cisco 3850

.. warning::

    Хотя технически использовать другое имя можно, всегда используйте
    self.

Во всех "обычных" методах класса первым параметром всегда будет self.
Кроме того, создание переменной экземпляра внутри класса также
выполняется через self.

Пример класса Switch с новым методом generate_interfaces: метод
generate_interfaces должен сгенерировать список с интерфейсами на
основании указанного типа и количества и создать переменную в экземпляре
класса. Для начала, вариант создания обычно переменной внутри метода:

.. code:: python

    In [5]: class Switch:
       ...:     def generate_interfaces(self, intf_type, number_of_intf):
       ...:         interfaces = [f"{intf_type}{number}" for number in range(1, number_of_intf + 1)]
       ...:

В этом случае, в экземплярах класса не будет переменной interfaces:

.. code:: python

    In [6]: sw1 = Switch()

    In [7]: sw1.generate_interfaces('Fa', 10)

    In [8]: sw1.interfaces
    ---------------------------------------------------------------------------
    AttributeError                            Traceback (most recent call last)
    <ipython-input-8-e6b457e4e23e> in <module>()
    ----> 1 sw1.interfaces

    AttributeError: 'Switch' object has no attribute 'interfaces'

Этой переменной нет, потому что она существует только внутри метода, а
область видимости у метода такая же, как и у функции. Даже другие методы
одного и того же класса, не видят переменные в других методах.

Чтобы список с интерфейсами был доступен как переменная в экземплярах,
надо присвоить значение в self.interfaces:

.. code:: python

    In [9]: class Switch:
       ...:     def info(self):
       ...:         print(f"Hostname: {self.hostname}\nModel: {self.model}")
       ...:
       ...:     def generate_interfaces(self, intf_type, number_of_intf):
       ...:         interfaces = [f"{intf_type}{number}" for number in range(1, number_of_intf+1)]
       ...:         self.interfaces = interfaces
       ...:

Теперь, после вызова метода generate_interfaces, в экземпляре создается
переменная interfaces:

.. code:: python

    In [10]: sw1 = Switch()

    In [11]: sw1.generate_interfaces('Fa', 10)

    In [12]: sw1.interfaces
    Out[12]: ['Fa1', 'Fa2', 'Fa3', 'Fa4', 'Fa5', 'Fa6', 'Fa7', 'Fa8', 'Fa9', 'Fa10']

