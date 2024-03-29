.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

.. _iterable:

Итерируемый объект
------------------

Итерация - это общий термин, который описывает процедуру взятия
элементов чего-то по очереди.

В более общем смысле, это последовательность инструкций, которая
повторяется определенное количество раз или до выполнения указанного
условия.

Итерируемый объект (iterable) - это объект, который способен возвращать
элементы по одному. Кроме того, это объект, из которого можно получить
итератор.

Примеры итерируемых объектов: 

* все последовательности: список, строка, кортеж 
* словари 
* файлы

В Python за получение итератора отвечает функция iter():

.. code:: python

    In [1]: lista = [1, 2, 3]

    In [2]: iter(lista)
    Out[2]: <list_iterator at 0xb4ede28c>

Функция ``iter()`` отработает на любом объекте, у которого есть метод
``__iter__`` или метод ``__getitem__``.

Метод ``__iter__`` возвращает итератор. Если этого метода нет,
функция iter() проверяет, нет ли метода ``__getitem__`` - метода,
который позволяет получать элементы по индексу.

Если метод ``__getitem__`` есть, возвращается итератор, который
проходится по элементам, используя индекс (начиная с 0).

На практике использование метода ``__getitem__`` означает, что все
последовательности элементов - это итерируемые объекты. Например,
список, кортеж, строка. Хотя у этих типов данных есть и метод
``__iter__``.
