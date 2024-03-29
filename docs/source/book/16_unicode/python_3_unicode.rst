.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

Юникод в Python 3
-----------------

В Python 3 есть: 

* строки - неизменяемая последовательность Unicode-символов.
  Для хранения этих символов используется тип строка (str) 
* байты - неизменяемая последовательность байтов. Для хранения
  используется тип bytes

Строки
~~~~~~

Примеры строк:

.. code:: python

    In [11]: hi = 'привет'

    In [12]: hi
    Out[12]: 'привет'

    In [15]: type(hi)
    Out[15]: str

    In [13]: beautiful = 'schön'

    In [14]: beautiful
    Out[14]: 'schön'

Так как строки - это последовательность кодов Юникод, можно записать
строку разными способами.

Символ Юникод можно записать, используя его имя:

.. code:: python

    In [1]: "\N{LATIN SMALL LETTER O WITH DIAERESIS}"
    Out[1]: 'ö'

Или использовав такой формат:

.. code:: python

    In [4]: "\u00F6"
    Out[4]: 'ö'

Строку можно записать как последовательность кодов Юникод:

.. code:: python

    In [19]: hi1 = 'привет'

    In [20]: hi2 = '\u043f\u0440\u0438\u0432\u0435\u0442'

    In [21]: hi2
    Out[21]: 'привет'

    In [22]: hi1 == hi2
    Out[22]: True

    In [23]: len(hi2)
    Out[23]: 6

Функция ord возвращает значение кода Unicode для символа:

.. code:: python

    In [6]: ord('ö')
    Out[6]: 246

Функция chr возвращает символ Юникод, который соответствует коду:

.. code:: python

    In [7]: chr(246)
    Out[7]: 'ö'

Байты
~~~~~

Тип bytes - это неизменяемая последовательность байтов.

Байты обозначаются так же, как строки, но с добавлением буквы "b" перед
строкой:

.. code:: python

    In [30]: b1 = b'\xd0\xb4\xd0\xb0'

    In [31]: b2 = b"\xd0\xb4\xd0\xb0"

    In [32]: b3 = b'''\xd0\xb4\xd0\xb0'''

    In [36]: type(b1)
    Out[36]: bytes

    In [37]: len(b1)
    Out[37]: 4

В Python байты, которые соответствуют символам ASCII, отображаются как
эти символы, а не как соответствующие им байты. Это может немного
путать, но всегда можно распознать тип bytes по букве b:

.. code:: python

    In [38]: bytes1 = b'hello'

    In [39]: bytes1
    Out[39]: b'hello'

    In [40]: len(bytes1)
    Out[40]: 5

    In [41]: bytes1.hex()
    Out[41]: '68656c6c6f'

    In [42]: bytes2 = b'\x68\x65\x6c\x6c\x6f'

    In [43]: bytes2
    Out[43]: b'hello'

Если попытаться написать не ASCII-символ в байтовом литерале, возникнет
ошибка:

.. code:: python

    In [44]: bytes3 = b'привет'
      File "<ipython-input-44-dc8b23504fa7>", line 1
        bytes3 = b'привет'
                ^
    SyntaxError: bytes can only contain ASCII literal characters.
