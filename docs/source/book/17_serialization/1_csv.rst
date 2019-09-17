Работа с файлами в формате CSV
------------------------------

**CSV (comma-separated value)** - это формат представления табличных
данных (например, это могут быть данные из таблицы или данные из БД).

В этом формате каждая строка файла - это строка таблицы. Несмотря на
название формата, разделителем может быть не только запятая.

И хотя у форматов с другим разделителем может быть и собственное
название, например, TSV (tab separated values), тем не менее, под
форматом CSV понимают, как правило, любые разделители.

Пример файла в формате CSV (sw_data.csv):

::

    hostname,vendor,model,location
    sw1,Cisco,3750,London
    sw2,Cisco,3850,Liverpool
    sw3,Cisco,3650,Liverpool
    sw4,Cisco,3650,London

В стандартной библиотеке Python есть модуль csv, который позволяет
работать с файлами в CSV формате.

Чтение
~~~~~~

Пример чтения файла в формате CSV (файл csv_read.py):

.. literalinclude:: /pyneng-examples-exercises/examples/17_serialization/csv/csv_read.py
  :language: python
  :linenos:


Вывод будет таким:

::

    $ python csv_read.py
    ['hostname', 'vendor', 'model', 'location']
    ['sw1', 'Cisco', '3750', 'London']
    ['sw2', 'Cisco', '3850', 'Liverpool']
    ['sw3', 'Cisco', '3650', 'Liverpool']
    ['sw4', 'Cisco', '3650', 'London']

В первом списке находятся названия столбцов, а в остальных
соответствующие значения.

Обратите внимание, что сам csv.reader возвращает итератор:

.. code:: python

    In [1]: import csv

    In [2]: with open('sw_data.csv') as f:
       ...:     reader = csv.reader(f)
       ...:     print(reader)
       ...:
    <_csv.reader object at 0x10385b050>

При необходимости его можно превратить в список таким образом:

.. code:: python

    In [3]: with open('sw_data.csv') as f:
       ...:     reader = csv.reader(f)
       ...:     print(list(reader))
       ...:
    [['hostname', 'vendor', 'model', 'location'], ['sw1', 'Cisco', '3750', 'London'], ['sw2', 'Cisco', '3850', 'Liverpool'], ['sw3', 'Cisco', '3650', 'Liverpool'], ['sw4', 'Cisco', '3650', 'London']]

Чаще всего заголовки столбцов удобней получить отдельным объектом. Это
можно сделать таким образом (файл csv_read_headers.py):

.. literalinclude:: /pyneng-examples-exercises/examples/17_serialization/csv/csv_read_headers.py
  :language: python
  :linenos:


Иногда в результате обработки гораздо удобней получить словари, в
которых ключи - это названия столбцов, а значения - значения столбцов.

Для этого в модуле есть **DictReader** (файл csv_read_dict.py):

.. literalinclude:: /pyneng-examples-exercises/examples/17_serialization/csv/csv_read_dict.py
  :language: python
  :linenos:


Вывод будет таким:

::

    $ python csv_read_dict.py
    OrderedDict([('hostname', 'sw1'), ('vendor', 'Cisco'), ('model', '3750'), ('location', 'London')])
    sw1 3750
    OrderedDict([('hostname', 'sw2'), ('vendor', 'Cisco'), ('model', '3850'), ('location', 'Liverpool')])
    sw2 3850
    OrderedDict([('hostname', 'sw3'), ('vendor', 'Cisco'), ('model', '3650'), ('location', 'Liverpool')])
    sw3 3650
    OrderedDict([('hostname', 'sw4'), ('vendor', 'Cisco'), ('model', '3650'), ('location', 'London')])
    sw4 3650

DictReader создает не стандартные словари Python, а упорядоченные
словари. За счет этого порядок элементов соответствует порядку столбцов
в CSV-файле.

.. note::

    До Python 3.6 возвращались обычные словари, а не упорядоченные.

В остальном с упорядоченными словарями можно работать, используя те же
методы, что и в обычных словарях.

Запись
~~~~~~

Аналогичным образом с помощью модуля csv можно и записать файл в формате
CSV (файл csv_write.py):

.. literalinclude:: /pyneng-examples-exercises/examples/17_serialization/csv/csv_write.py
  :language: python
  :linenos:


В примере выше строки из списка сначала записываются в файл, а затем
содержимое файла выводится на стандартный поток вывода.

Вывод будет таким:

::

    $ python csv_write.py
    hostname,vendor,model,location
    sw1,Cisco,3750,"London, Best str"
    sw2,Cisco,3850,"Liverpool, Better str"
    sw3,Cisco,3650,"Liverpool, Better str"
    sw4,Cisco,3650,"London, Best str"

Обратите внимание на интересную особенность: строки в последнем столбце
взяты в кавычки, а остальные значения - нет.

Так получилось из-за того, что во всех строках последнего столбца есть
запятая. И кавычки указывают на то, что именно является целой строкой.
Когда запятая находится в кавычках, модуль csv не воспринимает её как
разделитель.

Иногда лучше, чтобы все строки были в кавычках. Конечно, в данном случае
достаточно простой пример, но когда в строках больше значений, то
кавычки позволяют указать, где начинается и заканчивается значение.

Модуль csv позволяет управлять этим. Для того, чтобы все строки
записывались в CSV-файл с кавычками, надо изменить скрипт таким образом
(файл csv_write_quoting.py):

.. literalinclude:: /pyneng-examples-exercises/examples/17_serialization/csv/csv_write_quoting.py
  :language: python
  :linenos:


Теперь вывод будет таким:

::

    $ python csv_write_quoting.py
    "hostname","vendor","model","location"
    "sw1","Cisco","3750","London, Best str"
    "sw2","Cisco","3850","Liverpool, Better str"
    "sw3","Cisco","3650","Liverpool, Better str"
    "sw4","Cisco","3650","London, Best str"

Теперь все значения с кавычками. И поскольку номер модели задан как
строка в изначальном списке, тут он тоже в кавычках.

Кроме метода writerow, поддерживается метод writerows. Ему можно
передать любой итерируемый объект.

Например, предыдущий пример можно записать таким образом (файл
csv_writerows.py):

.. literalinclude:: /pyneng-examples-exercises/examples/17_serialization/csv/csv_writerows.py
  :language: python
  :linenos:


DictWriter
^^^^^^^^^^

С помощью DictWriter можно записать словари в формат CSV.

В целом DictWriter работает так же, как writer, но так как словари не
упорядочены, надо указывать явно в каком порядке будут идти столбцы в
файле. Для этого используется параметр fieldnames (файл
csv_write_dict.py):

.. literalinclude:: /pyneng-examples-exercises/examples/17_serialization/csv/csv_write_dict.py
  :language: python
  :linenos:


Указание разделителя
~~~~~~~~~~~~~~~~~~~~

Иногда в качестве разделителя используются другие значения. В таком
случае должна быть возможность подсказать модулю, какой именно
разделитель использовать.

Например, если в файле используется разделитель ``;`` (файл
sw_data2.csv):

::

    hostname;vendor;model;location
    sw1;Cisco;3750;London
    sw2;Cisco;3850;Liverpool
    sw3;Cisco;3650;Liverpool
    sw4;Cisco;3650;London

Достаточно просто указать, какой разделитель используется в reader (файл
csv_read_delimiter.py):

.. literalinclude:: /pyneng-examples-exercises/examples/17_serialization/csv/csv_read_delimiter.py
  :language: python
  :linenos:

