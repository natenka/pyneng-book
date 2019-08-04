Контроль символов whitespace
----------------------------

trim_blocks, lstrip_blocks
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Параметр ``trim_blocks`` удаляет первую пустую строку после блока
конструкции, если его значение равно True (по умолчанию False).

Эффект применения флага рассматривается на примере шаблона
templates/env_flags.txt:

::

    router bgp {{ bgp.local_as }}
     {% for ibgp in bgp.ibgp_neighbors %}
     neighbor {{ ibgp }} remote-as {{ bgp.local_as }}
     neighbor {{ ibgp }} update-source {{ bgp.loopback }}
     {% endfor %}

Если скрипт cfg_gen.py запускается без флагов trim_blocks,
lstrip_blocks:

.. code:: python

    env = Environment(loader=FileSystemLoader(TEMPLATE_DIR))

Вывод будет таким:

::

    $ python cfg_gen.py templates/env_flags.txt data_files/router.yml
    router bgp 100

     neighbor 10.0.0.2 remote-as 100
     neighbor 10.0.0.2 update-source lo100

     neighbor 10.0.0.3 remote-as 100
     neighbor 10.0.0.3 update-source lo100

Переводы строк появляются из-за блока for.

::

    {% for ibgp in bgp.ibgp_neighbors %}

По умолчанию такое же поведение будет с любыми другими блоками Jinja.

При добавлении флага trim_blocks таким образом:

.. code:: python

    env = Environment(loader=FileSystemLoader(TEMPLATE_DIR),
                      trim_blocks=True)

Результат выполнения будет таким:

::

    $ python cfg_gen.py templates/env_flags.txt data_files/router.yml
    router bgp 100
      neighbor 10.0.0.2 remote-as 100
     neighbor 10.0.0.2 update-source lo100
      neighbor 10.0.0.3 remote-as 100
     neighbor 10.0.0.3 update-source lo100

Были удалены пустые строки после блока.

Перед строками ``neighbor ... remote-as`` появились два пробела. Так
получилось из-за того, что перед блоком for стоит пробел. После того,
как был отключен лишний перевод строки, пробелы и табы перед блоком
добавляются к первой строке блока.

Это не влияет на следующие строки. Поэтому строки с
``neighbor ... update-source`` отображаются с одним пробелом.

Параметр ``lstrip_blocks`` контролирует то, будут ли удаляться пробелы и
табы от начала строки до начала блока (до открывающейся фигурной
скобки).

Если добавить аргумент ``lstrip_blocks=True`` таким образом:

.. code:: python

    env = Environment(loader=FileSystemLoader(TEMPLATE_DIR),
                      trim_blocks=True, lstrip_blocks=True)

Результат выполнения будет таким:

::

    $ python cfg_gen.py templates/env_flags.txt data_files/router.yml
    router bgp 100
     neighbor 10.0.0.2 remote-as 100
     neighbor 10.0.0.2 update-source lo100
     neighbor 10.0.0.3 remote-as 100
     neighbor 10.0.0.3 update-source lo100

Отключение lstrip_blocks для блока
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Иногда нужно отключить функциональность lstrip_blocks для блока.

Например, если параметр ``lstrip_blocks`` установлен равным True в
окружении, но нужно отключить его для второго блока в шаблоне (файл
templates/env_flags2.txt):

::

    router bgp {{ bgp.local_as }}
     {% for ibgp in bgp.ibgp_neighbors %}
     neighbor {{ ibgp }} remote-as {{ bgp.local_as }}
     neighbor {{ ibgp }} update-source {{ bgp.loopback }}
     {% endfor %}

    router bgp {{ bgp.local_as }}
     {%+ for ibgp in bgp.ibgp_neighbors %}
     neighbor {{ ibgp }} remote-as {{ bgp.local_as }}
     neighbor {{ ibgp }} update-source {{ bgp.loopback }}
     {% endfor %}

Результат будет таким:

::

    $ python cfg_gen.py templates/env_flags2.txt data_files/router.yml
    router bgp 100
     neighbor 10.0.0.2 remote-as 100
     neighbor 10.0.0.2 update-source lo100
     neighbor 10.0.0.3 remote-as 100
     neighbor 10.0.0.3 update-source lo100

    router bgp 100
      neighbor 10.0.0.2 remote-as 100
     neighbor 10.0.0.2 update-source lo100
     neighbor 10.0.0.3 remote-as 100
     neighbor 10.0.0.3 update-source lo100

Плюс после знака процента отключает lstrip_blocks для блока, в данном
случае, только для начала блока.

Если сделать таким образом (плюс добавлен в выражении для завершения
блока):

::

    router bgp {{ bgp.local_as }}
     {% for ibgp in bgp.ibgp_neighbors %}
     neighbor {{ ibgp }} remote-as {{ bgp.local_as }}
     neighbor {{ ibgp }} update-source {{ bgp.loopback }}
     {% endfor %}

    router bgp {{ bgp.local_as }}
     {%+ for ibgp in bgp.ibgp_neighbors %}
     neighbor {{ ibgp }} remote-as {{ bgp.local_as }}
     neighbor {{ ibgp }} update-source {{ bgp.loopback }}
     {%+ endfor %}

Он будет отключен и для конца блока:

::

    $ python cfg_gen.py templates/env_flags2.txt data_files/router.yml
    router bgp 100
     neighbor 10.0.0.2 remote-as 100
     neighbor 10.0.0.2 update-source lo100
     neighbor 10.0.0.3 remote-as 100
     neighbor 10.0.0.3 update-source lo100

    router bgp 100
      neighbor 10.0.0.2 remote-as 100
     neighbor 10.0.0.2 update-source lo100
      neighbor 10.0.0.3 remote-as 100
     neighbor 10.0.0.3 update-source lo100

Удаление whitespace в блоке
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Аналогичным образом можно контролировать удаление whitespace для блока.

Для этого примера в окружении не выставлены флаги:

::

    env = Environment(loader=FileSystemLoader(TEMPLATE_DIR))

Шаблон templates/env_flags3.txt:

::

    router bgp {{ bgp.local_as }}
     {% for ibgp in bgp.ibgp_neighbors %}
     neighbor {{ ibgp }} remote-as {{ bgp.local_as }}
     neighbor {{ ibgp }} update-source {{ bgp.loopback }}
     {% endfor %}

    router bgp {{ bgp.local_as }}
     {%- for ibgp in bgp.ibgp_neighbors %}
     neighbor {{ ibgp }} remote-as {{ bgp.local_as }}
     neighbor {{ ibgp }} update-source {{ bgp.loopback }}
     {% endfor %}

Обратите внимание на минус в начале второго блока. Минус удаляет все
whitespace символы, в данном случае, в начале блока.

Результат будет таким:

::

    $ python cfg_gen.py templates/env_flags3.txt data_files/router.yml
    router bgp 100

     neighbor 10.0.0.2 remote-as 100
     neighbor 10.0.0.2 update-source lo100

     neighbor 10.0.0.3 remote-as 100
     neighbor 10.0.0.3 update-source lo100


    router bgp 100
     neighbor 10.0.0.2 remote-as 100
     neighbor 10.0.0.2 update-source lo100

     neighbor 10.0.0.3 remote-as 100
     neighbor 10.0.0.3 update-source lo100

Если добавить минус в конец блока:

::

    router bgp {{ bgp.local_as }}
     {% for ibgp in bgp.ibgp_neighbors %}
     neighbor {{ ibgp }} remote-as {{ bgp.local_as }}
     neighbor {{ ibgp }} update-source {{ bgp.loopback }}
     {% endfor %}

    router bgp {{ bgp.local_as }}
     {%- for ibgp in bgp.ibgp_neighbors %}
     neighbor {{ ibgp }} remote-as {{ bgp.local_as }}
     neighbor {{ ibgp }} update-source {{ bgp.loopback }}
     {%- endfor %}

Удалится пустая строка и в конце блока:

::

    $ python cfg_gen.py templates/env_flags3.txt data_files/router.yml
    router bgp 100

     neighbor 10.0.0.2 remote-as 100
     neighbor 10.0.0.2 update-source lo100

     neighbor 10.0.0.3 remote-as 100
     neighbor 10.0.0.3 update-source lo100


    router bgp 100
     neighbor 10.0.0.2 remote-as 100
     neighbor 10.0.0.2 update-source lo100
     neighbor 10.0.0.3 remote-as 100
     neighbor 10.0.0.3 update-source lo100

Попробуйте добавить минус в конце выражений, описывающих блок, и
посмотреть на результат:

::

    router bgp {{ bgp.local_as }}
     {% for ibgp in bgp.ibgp_neighbors %}
     neighbor {{ ibgp }} remote-as {{ bgp.local_as }}
     neighbor {{ ibgp }} update-source {{ bgp.loopback }}
     {% endfor %}

    router bgp {{ bgp.local_as }}
     {%- for ibgp in bgp.ibgp_neighbors -%}
     neighbor {{ ibgp }} remote-as {{ bgp.local_as }}
     neighbor {{ ibgp }} update-source {{ bgp.loopback }}
     {%- endfor -%}

