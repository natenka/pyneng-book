Варианты создания словаря
-------------------------

Литерал
~~~~~~~

Словарь можно создать с помощью литерала:

.. code:: python

    In [1]: r1 = {'model': '4451', 'ios': '15.4'}

dict
~~~~

Конструктор **dict** позволяет создавать словарь несколькими способами.

Если в роли ключей используются строки, можно использовать такой вариант
создания словаря:

.. code:: python

    In [2]: r1 = dict(model='4451', ios='15.4')

    In [3]: r1
    Out[3]: {'ios': '15.4', 'model': '4451'}

Второй вариант создания словаря с помощью dict:

.. code:: python

    In [4]: r1 = dict([('model','4451'), ('ios','15.4')])

    In [5]: r1
    Out[5]: {'ios': '15.4', 'model': '4451'}

dict.fromkeys
~~~~~~~~~~~~~

В ситуации, когда надо создать словарь с известными ключами, но, пока
что, пустыми значениями (или одинаковыми значениями), очень удобен метод
**fromkeys()**:

.. code:: python

    In [5]: d_keys = ['hostname', 'location', 'vendor', 'model', 'ios', 'ip']

    In [6]: r1 = dict.fromkeys(d_keys)

    In [7]: r1
    Out[7]: 
    {'ios': None,
     'ip': None,
     'hostname': None,
     'location': None,
     'model': None,
     'vendor': None}

По умолчанию, метод fromkeys подставляет значение None. Но можно
указывать и свой вариант значения:

.. code:: python

    In [8]: router_models = ['ISR2811', 'ISR2911', 'ISR2921', 'ASR9002']

    In [9]: models_count = dict.fromkeys(router_models, 0)

    In [10]: models_count
    Out[10]: {'ASR9002': 0, 'ISR2811': 0, 'ISR2911': 0, 'ISR2921': 0}

Этот вариант создания словаря подходит не для всех случаев. Например,
при использовании изменяемого типа данных в значении, будет создана
ссылка на один и тот же объект:

.. code:: python

    In [11]: router_models = ['ISR2811', 'ISR2911', 'ISR2921', 'ASR9002']

    In [12]: routers = dict.fromkeys(router_models, [])

    In [13]: routers
    Out[13]: {'ASR9002': [], 'ISR2811': [], 'ISR2911': [], 'ISR2921': []}

    In [14]: routers['ASR9002'].append('london_r1')

    In [15]: routers
    Out[15]:
    {'ASR9002': ['london_r1'],
     'ISR2811': ['london_r1'],
     'ISR2911': ['london_r1'],
     'ISR2921': ['london_r1']}

В данном случае каждый ключ ссылается на один и тот же список. Поэтому,
при добавлении значения в один из списков обновляются и остальные.

Генератор словаря (dict comprehensions)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

И последний метод создания словаря - **генераторы словарей**.

Сгенерируем словарь со списками в значении, как в предыдущем примере:

.. code:: python

    In [16]: router_models = ['ISR2811', 'ISR2911', 'ISR2921', 'ASR9002']

    In [17]: routers = {key: [] for key in router_models}

    In [18]: routers
    Out[18]: {'ASR9002': [], 'ISR2811': [], 'ISR2911': [], 'ISR2921': []}

    In [19]: routers['ASR9002'].append('london_r1')

    In [20]: routers
    Out[20]: {'ASR9002': ['london_r1'], 'ISR2811': [], 'ISR2911': [], 'ISR2921': []}

