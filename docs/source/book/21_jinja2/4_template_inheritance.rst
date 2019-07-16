Наследование шаблонов
---------------------

Наследование шаблонов - это очень мощный функционал, который позволяет
избежать повторения одного и того же в разных шаблонах.

При использовании наследования различают: 

* **базовый шаблон** - это шаблон, в котором описывается каркас шаблона. 
* в этом шаблоне могут находиться любые обычные выражения или текст. 
  Кроме того, в этом шаблоне определяются специальные **блоки (block)**. 
* **дочерний шаблон** - шаблон, который расширяет базовый шаблон, заполняя
  обозначенные блоки. 
* дочерние шаблоны могут переписывать или дополнять
  блоки, определенные в базовом шаблоне.

Пример базового шаблона templates/base_router.txt:

::

    !
    {% block services %}
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    {% endblock %}
    !
    no ip domain lookup
    !
    ip ssh version 2
    !
    {% block ospf %}
    router ospf 1
     auto-cost reference-bandwidth 10000
    {% endblock %}
    !
    {% block bgp %}
    {% endblock %}
    !
    {% block alias %}
    {% endblock %}
    !
    line con 0
     logging synchronous
     history size 100
    line vty 0 4
     logging synchronous
     history size 100
     transport input ssh
    !

Обратите внимание на четыре блока, которые созданы в шаблоне:

::

    {% block services %}
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    {% endblock %}
    !
    {% block ospf %}
    router ospf 1
     auto-cost reference-bandwidth 10000
    {% endblock %}
    !
    {% block bgp %}
    {% endblock %}
    !
    {% block alias %}
    {% endblock %}

Это заготовки для соответствующих разделов конфигурации. Дочерний
шаблон, который будет использовать этот базовый шаблон как основу, может
заполнять все блоки или только какие-то из них.

Дочерний шаблон templates/hq_router.txt:

::

    {% extends "base_router.txt" %}

    {% block ospf %}
    {{ super() }}
    {% for networks in ospf %}
     network {{ networks.network }} area {{ networks.area }}
    {% endfor %}
    {% endblock %}

    {% block alias %}
    alias configure sh do sh
    alias exec ospf sh run | s ^router ospf
    alias exec bri show ip int bri | exc unass
    alias exec id show int desc
    alias exec top sh proc cpu sorted | excl 0.00%__0.00%__0.00%
    alias exec c conf t
    alias exec diff sh archive config differences nvram:startup-config system:running-config
    alias exec desc sh int desc | ex down
    {% endblock %}

Первая строка в шаблоне templates/hq_router.txt очень важна:

::

    {% extends "base_router.txt" %}

Именно она говорит о том, что шаблон hq_router.txt будет построен на
основе шаблона base_router.txt.

Внутри дочернего шаблона всё происходит внутри блоков. За счет блоков,
которые были определены в базовом шаблоне, дочерний шаблон может
расширять родительский шаблон.

.. note::

    Обратите внимание, что те строки, которые описаны в дочернем шаблоне
    за пределами блоков, игнорируются.

В базовом шаблоне четыре блока: services, ospf, bgp, alias. В дочернем
шаблоне заполнены только два из них: ospf и alias.
В этом удобство наследования. Не обязательно заполнять все блоки в
каждом дочернем шаблоне.

При этом блоки ospf и alias используются по-разному. В базовом шаблоне в
блоке ospf уже была часть конфигурации:

::

    {% block ospf %}
    router ospf 1
     auto-cost reference-bandwidth 10000
    {% endblock %}

Поэтому, в дочернем шаблоне есть выбор: использовать эту конфигурацию и
дополнить её, или полностью переписать всё в дочернем шаблоне.

В данном случае конфигурация дополняется. Именно поэтому в дочернем
шаблоне templates/hq_router.txt блок ospf начинается с выражения
``{{ super() }}``:

::

    {% block ospf %}
    {{ super() }}
     {% for networks in ospf %}
     network {{ networks.network }} area {{ networks.area }}
     {% endfor %}
    {% endblock %}

``{{ super() }}`` переносит в дочерний шаблон содержимое этого блока из
родительского шаблона. За счет этого в дочерний шаблон перенесутся
строки из родительского.

.. note::

    Выражение super не обязательно должно находиться в самом начале
    блока. Оно может быть в любом месте блока. Содержимое базового
    шаблона перенесется в то место, где находится выражение super.

В блоке alias просто описаны нужные alias. И, даже если бы в
родительском шаблоне были какие-то настройки, они были бы затерты
содержимым дочернего шаблона.

Подытожим правила работы с блоками. Если в родительском шаблоне создан
блок: 

* без содержимого - в дочернем шаблоне можно заполнить этот блок
  или игнорировать. Если блок заполнен, в нём будет только то, что было
  написано в дочернем шаблоне (пример - блок alias) 
* с содержимым - то в дочернем шаблоне можно выполнить такие действия: 

  * игнорировать блок - в таком случае в дочерний шаблон попадет содержимое, 
    которое находилось в этом блоке в родительском шаблоне (пример - блок services) 
  * переписать блок - тогда в дочернем шаблоне будет только то, что указано в нём 
  * перенести содержимое блока из родительского шаблона и дополнить 
    его - тогда в дочернем шаблоне будет и содержимое блока из родительского
    шаблона, и содержимое из дочернего шаблона. Для переноса содержимого из
    родительского шаблона используется выражение ``{{ super() }}`` (пример - блок ospf)

Файл с данными для генерации конфигурации по шаблону
(data_files/hq_router.yml):

.. code:: json

    ospf:
      - network: 10.0.1.0 0.0.0.255
        area: 0
      - network: 10.0.2.0 0.0.0.255
        area: 2
      - network: 10.1.1.0 0.0.0.255
        area: 0

Результат выполнения будет таким:

::

    $ python cfg_gen.py templates/hq_router.txt data_files/hq_router.yml
    !
    service timestamps debug datetime msec localtime show-timezone year
    service timestamps log datetime msec localtime show-timezone year
    service password-encryption
    service sequence-numbers
    !
    no ip domain lookup
    !
    ip ssh version 2
    !
    router ospf 1
     auto-cost reference-bandwidth 10000

     network 10.0.1.0 0.0.0.255 area 0
     network 10.0.2.0 0.0.0.255 area 2
     network 10.1.1.0 0.0.0.255 area 0
    !
    !
    alias configure sh do sh
    alias exec ospf sh run | s ^router ospf
    alias exec bri show ip int bri | exc unass
    alias exec id show int desc
    alias exec top sh proc cpu sorted | excl 0.00%__0.00%__0.00%
    alias exec c conf t
    alias exec diff sh archive config differences nvram:startup-config system:running-config
    alias exec desc sh int desc | ex down
    !
    line con 0
     logging synchronous
     history size 100
    line vty 0 4
     logging synchronous
     history size 100
     transport input ssh
    !

Обратите внимание, что в блоке ospf есть и команды из базового шаблона,
и команды из дочернего шаблона.

