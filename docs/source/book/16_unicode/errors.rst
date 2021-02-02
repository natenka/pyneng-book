Ошибки при конвертации
----------------------

При конвертации между строками и байтами очень важно точно знать, какая
кодировка используется, а также знать о возможностях разных кодировок.

Например, кодировка ASCII не может преобразовать в байты кириллицу:

.. code:: python

    In [32]: hi_unicode = 'привет'

    In [33]: hi_unicode.encode('ascii')
    ---------------------------------------------------------------------------
    UnicodeEncodeError                        Traceback (most recent call last)
    <ipython-input-33-ec69c9fd2dae> in <module>()
    ----> 1 hi_unicode.encode('ascii')

    UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-5: ordinal not in range(128)

Аналогично, если строка "привет" преобразована в байты, и попробовать
преобразовать ее в строку с помощью ascii, тоже получим ошибку:

.. code:: python


    In [34]: hi_unicode = 'привет'

    In [35]: hi_bytes = hi_unicode.encode('utf-8')

    In [36]: hi_bytes.decode('ascii')
    ---------------------------------------------------------------------------
    UnicodeDecodeError                        Traceback (most recent call last)
    <ipython-input-36-aa0ada5e44e9> in <module>()
    ----> 1 hi_bytes.decode('ascii')

    UnicodeDecodeError: 'ascii' codec can't decode byte 0xd0 in position 0: ordinal not in range(128)

Еще один вариант ошибки, когда используются разные кодировки для
преобразований:

.. code:: python


    In [37]: de_hi_unicode = 'grüezi'

    In [38]: utf_16 = de_hi_unicode.encode('utf-16')

    In [39]: utf_16.decode('utf-8')
    ---------------------------------------------------------------------------
    UnicodeDecodeError                        Traceback (most recent call last)
    <ipython-input-39-4b4c731e69e4> in <module>()
    ----> 1 utf_16.decode('utf-8')

    UnicodeDecodeError: 'utf-8' codec can't decode byte 0xff in position 0: invalid start byte

Наличие ошибок - это хорошо. Они явно говорят, в чем проблема.
Хуже, когда получается так:

.. code:: python

    In [40]: hi_unicode = 'привет'

    In [41]: hi_bytes = hi_unicode.encode('utf-8')

    In [42]: hi_bytes
    Out[42]: b'\xd0\xbf\xd1\x80\xd0\xb8\xd0\xb2\xd0\xb5\xd1\x82'

    In [43]: hi_bytes.decode('utf-16')
    Out[43]: '뿐胑룐닐뗐苑'

Обработка ошибок
~~~~~~~~~~~~~~~~

У методов encode и decode есть режимы обработки ошибок, которые
указывают, как реагировать на ошибку преобразования.

Параметр errors в encode
^^^^^^^^^^^^^^^^^^^^^^^^

По умолчанию encode использует режим ``strict`` - при возникновении ошибок
кодировки генерируется исключение UnicodeError. Примеры такого поведения
были выше.

Вместо этого режима можно использовать replace, чтобы заменить символ
знаком вопроса:

.. code:: python

    In [44]: de_hi_unicode = 'grüezi'

    In [45]: de_hi_unicode.encode('ascii', 'replace')
    Out[45]: b'gr?ezi'

Или namereplace, чтобы заменить символ именем:

.. code:: python

    In [46]: de_hi_unicode = 'grüezi'

    In [47]: de_hi_unicode.encode('ascii', 'namereplace')
    Out[47]: b'gr\\N{LATIN SMALL LETTER U WITH DIAERESIS}ezi'

Кроме того, можно полностью игнорировать символы, которые нельзя
закодировать:

.. code:: python

    In [48]: de_hi_unicode = 'grüezi'

    In [49]: de_hi_unicode.encode('ascii', 'ignore')
    Out[49]: b'grezi'

Параметр errors в decode
^^^^^^^^^^^^^^^^^^^^^^^^

В методе decode по умолчанию тоже используется режим strict и
генерируется исключение UnicodeDecodeError.

Если изменить режим на ignore, как и в encode, символы будут просто
игнорироваться:

.. code:: python

    In [50]: de_hi_unicode = 'grüezi'

    In [51]: de_hi_utf8 = de_hi_unicode.encode('utf-8')

    In [52]: de_hi_utf8
    Out[52]: b'gr\xc3\xbcezi'

    In [53]: de_hi_utf8.decode('ascii', 'ignore')
    Out[53]: 'grezi'

Режим replace заменит символы:

.. code:: python

    In [54]: de_hi_unicode = 'grüezi'

    In [55]: de_hi_utf8 = de_hi_unicode.encode('utf-8')

    In [56]: de_hi_utf8.decode('ascii', 'replace')
    Out[56]: 'gr��ezi'

