.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

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
    Out[3]: {'model': '4451', 'ios': '15.4'}

Второй вариант создания словаря с помощью dict:

.. code:: python

    In [4]: r1 = dict([('model', '4451'), ('ios', '15.4')])

    In [5]: r1
    Out[5]: {'model': '4451', 'ios': '15.4'}

dict.fromkeys
~~~~~~~~~~~~~

В ситуации, когда надо создать словарь с известными ключами, но пока
что пустыми значениями (или одинаковыми значениями), очень удобен метод
**fromkeys()**:

.. code:: python

    In [5]: d_keys = ['hostname', 'location', 'vendor', 'model', 'ios', 'ip']

    In [6]: r1 = dict.fromkeys(d_keys)

    In [7]: r1
    Out[7]:
    {'hostname': None,
     'location': None,
     'vendor': None,
     'model': None,
     'ios': None,
     'ip': None}


По умолчанию метод fromkeys подставляет значение None. Но можно
указывать и свой вариант значения:

.. code:: python

    In [8]: router_models = ['ISR2811', 'ISR2911', 'ISR2921', 'ASR9002']

    In [9]: models_count = dict.fromkeys(router_models, 0)

    In [10]: models_count
    Out[10]: {'ISR2811': 0, 'ISR2911': 0, 'ISR2921': 0, 'ASR9002': 0}


Этот вариант создания словаря подходит не для всех случаев. Например,
при использовании изменяемого типа данных в значении, будет создана
ссылка на один и тот же объект:

.. code:: python

    In [10]: router_models = ['ISR2811', 'ISR2911', 'ISR2921', 'ASR9002']

    In [11]: routers = dict.fromkeys(router_models, [])
        ...:

    In [12]: routers
    Out[12]: {'ISR2811': [], 'ISR2911': [], 'ISR2921': [], 'ASR9002': []}

    In [13]: routers['ASR9002'].append('london_r1')

    In [14]: routers
    Out[14]:
    {'ISR2811': ['london_r1'],
     'ISR2911': ['london_r1'],
     'ISR2921': ['london_r1'],
     'ASR9002': ['london_r1']}

В данном случае каждый ключ ссылается на один и тот же список. Поэтому,
при добавлении значения в один из списков обновляются и остальные.

.. note::
    Для такой задачи лучше подходит генератор словаря. Смотри раздел :ref:`x_comprehensions`
