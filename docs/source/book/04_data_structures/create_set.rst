.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

Варианты создания множества
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Нельзя создать пустое множество с помощью литерала (так как в таком
случае это будет не множество, а словарь):

.. code:: python

    In [1]: set1 = {}

    In [2]: type(set1)
    Out[2]: dict

Но пустое множество можно создать таким образом:

.. code:: python

    In [3]: set2 = set()

    In [4]: type(set2)
    Out[4]: set

Множество из строки:

.. code:: python

    In [5]: set('long long long long string')
    Out[5]: {' ', 'g', 'i', 'l', 'n', 'o', 'r', 's', 't'}

Множество из списка:

.. code:: python

    In [6]: set([10, 20, 30, 10, 10, 30])
    Out[6]: {10, 20, 30}
