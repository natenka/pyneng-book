Метод ``__init__``
~~~~~~~~~~~~~~~~~~

Для корректной работы метода info, необходимо чтобы у экземпляра были
переменные hostname и model. Если этих переменных нет, возникнет ошибка:

.. code:: python

    In [15]: class Switch:
        ...:     def info(self):
        ...:         print('Hostname: {}\nModel: {}'.format(self.hostname, self.model))
        ...:

    In [59]: sw2 = Switch()

    In [60]: sw2.info()
    ---------------------------------------------------------------------------
    AttributeError                            Traceback (most recent call last)
    <ipython-input-60-5a006dd8aae1> in <module>()
    ----> 1 sw2.info()

    <ipython-input-57-30b05739380d> in info(self)
          1 class Switch:
          2     def info(self):
    ----> 3         print('Hostname: {}\nModel: {}'.format(self.hostname, self.model))

    AttributeError: 'Switch' object has no attribute 'hostname'

Практически всегда, при создании объекта, у него есть какие-то начальные
данные. Например, чтобы создать подключение к оборудование с помощью
netmiko, надо передать параметры подключения.

В Python эти начальные данные про объект указываются в методе
``__init__``. Метод ``__init__`` выполняется после того как Python
создал новый экземпляр и, при этом, методу ``__init__`` передаются
аргументы с которыми был создан экземпляр:

.. code:: python

    In [32]: class Switch:
        ...:     def __init__(self, hostname, model):
        ...:         self.hostname = hostname
        ...:         self.model = model
        ...:
        ...:     def info(self):
        ...:         print(f'Hostname: {self.hostname}\nModel: {self.model}')
        ...:

Обратите внимание на то, что у каждого экземпляра, который создан из этого класса,
будут созданы переменные: ``self.model`` и ``self.hostname``.

Теперь, при создании экземпляра класса Switch, обязательно надо указать
hostname и model:

.. code:: python

    In [33]: sw1 = Switch('sw1', 'Cisco 3850')

И, соответственно, метод info отрабатывает без ошибок:

.. code:: python

    In [36]: sw1.info()
    Hostname: sw1
    Model: Cisco 3850

.. note::

    Метод ``__init__`` иногда называют конструктором класса, хотя
    технически в Python сначала выполняется метод ``__new__``, а затем
    ``__init__``. В большинстве случаев, метод ``__new__`` использовать
    не нужно.

Важной особенностью метода ``__init__`` является то, что он не должен
ничего возвращать. Python сгенерирует исключение, если попытаться это
сделать.

