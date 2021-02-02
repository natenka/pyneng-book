Исполняемый файл
~~~~~~~~~~~~~~~~

Для того, чтобы файл был исполняемым, и не нужно было каждый раз писать
python перед вызовом файла, нужно:

* сделать файл исполняемым (для Linux)
* **в первой строке файла** должна находиться строка ``#!/usr/bin/env python``
  или ``#!/usr/bin/env python3``, в зависимости от того,
  какая версия Python используется по умолчанию

Пример файла access_template_exec.py:

.. code:: python

    #!/usr/bin/env python3

    access_template = ['switchport mode access',
                       'switchport access vlan {}',
                       'switchport nonegotiate',
                       'spanning-tree portfast',
                       'spanning-tree bpduguard enable']

    print('\n'.join(access_template).format(5))

После этого:

::

    chmod +x access_template_exec.py

Теперь можно вызывать файл таким образом:

::

    $ ./access_template_exec.py

