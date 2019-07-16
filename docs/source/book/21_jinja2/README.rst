Начало работы с Jinja2
======================

Установить Jinja2 можно с помощью pip:

.. code:: python

    pip install jinja2

.. note::

    Далее термины Jinja и Jinja2 используются взаимозаменяемо.

Главная идея Jinja: разделение данных и шаблона. Это позволяет
использовать один и тот же шаблон, но подставлять в него разные данные.

В самом простом случае шаблон - это просто текстовый файл, в котором
указаны места подстановки значений с помощью переменных Jinja.

Пример шаблона Jinja:

.. code:: jinja

    hostname {{name}}
    !
    interface Loopback255
     description Management loopback
     ip address 10.255.{{id}}.1 255.255.255.255
    !
    interface GigabitEthernet0/0
     description LAN to {{name}} sw1 {{int}}
     ip address {{ip}} 255.255.255.0
    !
    router ospf 10
     router-id 10.255.{{id}}.1
     auto-cost reference-bandwidth 10000
     network 10.0.0.0 0.255.255.255 area 0

Комментарии к шаблону: 

* В Jinja переменные записываются в двойных фигурных скобках. 
* При выполнении скрипта эти переменные заменяются нужными значениями.

Этот шаблон может использоваться для генерации конфигурации разных
устройств с помощью подстановки других наборов переменных.

Пример скрипта с генерацией файла на основе шаблона Jinja (файл
basic_generator.py):

.. code:: python

    from jinja2 import Template

    template = Template('''
    hostname {{name}}
    !
    interface Loopback255
     description Management loopback
     ip address 10.255.{{id}}.1 255.255.255.255
    !
    interface GigabitEthernet0/0
     description LAN to {{name}} sw1 {{int}}
     ip address {{ip}} 255.255.255.0
    !
    router ospf 10
     router-id 10.255.{{id}}.1
     auto-cost reference-bandwidth 10000
     network 10.0.0.0 0.255.255.255 area 0
    ''')

    liverpool = {'id':'11', 'name':'Liverpool', 'int':'Gi1/0/17', 'ip':'10.1.1.10'}

    print(template.render(liverpool))

Комментарии к файлу basic_generator.py: 

* в первой строке из Jinja2 импортируется класс Template 
* создается объект template, которому передается шаблон 
* в шаблоне используются переменные в синтаксисе Jinja 
* в словаре liverpool ключи должны быть такими же, как имена переменных в шаблоне 
* значения, которые соответствуют ключам - это те данные,
  которые будут подставлены на место переменных 
* последняя строка рендерит шаблон, используя словарь liverpool, то есть,
  подставляет значения в переменные.

Если запустить скрипт basic_generator.py, то вывод будет таким:

::

    $ python basic_generator.py

    hostname Liverpool
    !
    interface Loopback255
     description Management loopback
     ip address 10.255.11.1 255.255.255.255
    !
    interface GigabitEthernet0/0
     description LAN to Liverpool sw1 Gi1/0/17
     ip address 10.1.1.10 255.255.255.0
    !
    router ospf 10
     router-id 10.255.11.1
     auto-cost reference-bandwidth 10000
     network 10.0.0.0 0.255.255.255 area 0

