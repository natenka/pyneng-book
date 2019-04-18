Переменные класса
~~~~~~~~~~~~~~~~~

Помимо переменных экземпляра, существуют также переменные класса. Они
создаются, при указании переменных внутри самого класса, не метода:

.. code:: python

    In [27]: class A:
        ...:     var_a = 5
        ...:
        ...:     def method(self):
        ...:         pass
        ...:

Теперь не только у класса, но и у каждого экземпляра класса будет
переменная ``var_a``:

.. code:: python

    In [40]: A.var_a
    Out[40]: 5

    In [30]: a1 = A()

    In [31]: a1.var_a
    Out[31]: 5

    In [32]: a2 = A()

    In [33]: a2.var_a
    Out[33]: 5

Важный момент при использовании переменных класса, то что внутри метода
к ним все равно надо обращаться через имя класса (или self, но через имя
класса лучше, так как тогда понятно, что это переменная класса). Для
начала, вариант обращения без имени класса:

.. code:: python

    In [37]: class A:
        ...:     var_a = 5
        ...:
        ...:     def method(self):
        ...:         print(var_a)
        ...:

    In [38]: a1 = A()

    In [39]: a1.method()
    ---------------------------------------------------------------------------
    NameError                                 Traceback (most recent call last)
    <ipython-input-39-921b8753dbee> in <module>()
    ----> 1 a1.method()

    <ipython-input-37-ef925c4e39d3> in method(self)
          3
          4     def method(self):
    ----> 5         print(var_a)
          6

    NameError: name 'var_a' is not defined

И правильный вариант:

.. code:: python

    In [47]: class A:
        ...:     var_a = 5
        ...:
        ...:     def method(self):
        ...:         print(A.var_a)
        ...:

    In [48]: a1 = A()

    In [49]: a1.method()
    5

