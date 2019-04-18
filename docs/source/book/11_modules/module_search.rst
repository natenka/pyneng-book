Пути поиска модулей
-------------------

При импорте модуля, Python ищет модуль в таком порядке: \* в текущем
каталоге \* если модуль не найден, Python ищет модуль в каталогах,
которые указаны в переменной PYTHONPATH \* если модуль не найден, Python
проверяет путь по умолчанию. В Unix это, как правило,
/usr/local/lib/python/

Пути поиска модулей хранятся в переменной sys.path:

.. code:: python

    In [1]: import sys

    In [2]: sys.path
    Out[2]: 
    ['',
     '/usr/local/bin',
     '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python27.zip',
     '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7',
     '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-darwin',
     '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-mac',
     '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/plat-mac/lib-scriptpackages',
     '/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python',
     '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-tk',
     '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-old',
     '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/lib-dynload',
     '/System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python/PyObjC',
     '/Library/Python/2.7/site-packages',
     '/Library/Python/2.7/site-packages/IPython/extensions',
     '/Users/natasha/.ipython']

