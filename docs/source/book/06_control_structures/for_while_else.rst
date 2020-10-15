for/else, while/else
--------------------

В циклах for и while опционально может использоваться блок else.

for/else
~~~~~~~~

В цикле for:

* блок else выполняется в том случае, если цикл завершил итерацию списка
* но else **не выполняется**, если в цикле был выполнен break

Пример цикла for с else (блок else выполняется после завершения цикла
for):

.. code:: python

    In [1]: for num in range(5):
       ....:     print(num)
       ....: else:
       ....:     print("Числа закончились")
       ....:     
    0
    1
    2
    3
    4
    Числа закончились

Пример цикла for с else и break в цикле (из-за break блок else не
выполняется):

.. code:: python

    In [2]: for num in range(5):
       ....:     if num == 3:
       ....:         break
       ....:     else:
       ....:         print(num)
       ....: else:
       ....:     print("Числа закончились")
       ....:     
    0
    1
    2

Пример цикла for с else и continue в цикле (continue не влияет на блок
else):

.. code:: python

    In [3]: for num in range(5):
       ....:     if num == 3:
       ....:         continue
       ....:     else:
       ....:         print(num)
       ....: else:
       ....:     print("Числа закончились")
       ....:     
    0
    1
    2
    4
    Числа закончились

while/else
~~~~~~~~~~

В цикле while:

* блок else выполняется в том случае, если цикл завершил итерацию списка
* но else **не выполняется**, если в цикле был выполнен break

Пример цикла while с else (блок else выполняется после завершения цикла
while):

.. code:: python

    In [4]: i = 0
    In [5]: while i < 5:
       ....:     print(i)
       ....:     i += 1
       ....: else:
       ....:     print("Конец")
       ....:     
    0
    1
    2
    3
    4
    Конец

Пример цикла while с else и break в цикле (из-за break блок else не
выполняется):

.. code:: python

    In [6]: i = 0

    In [7]: while i < 5:
       ....:     if i == 3:
       ....:         break
       ....:     else:
       ....:         print(i)
       ....:         i += 1
       ....: else:
       ....:     print("Конец")
       ....:     
    0
    1
    2

