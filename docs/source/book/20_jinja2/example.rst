Пример использования Jinja
--------------------------

Шаблон templates/router_template.txt - это обычный текстовый файл:

::

    hostname {{name}}
    !
    interface Loopback10
     description MPLS loopback
     ip address 10.10.{{id}}.1 255.255.255.255
     !
    interface GigabitEthernet0/0
     description WAN to {{name}} sw1 G0/1
    !
    interface GigabitEthernet0/0.1{{id}}1
     description MPLS to {{to_name}}
     encapsulation dot1Q 1{{id}}1
     ip address 10.{{id}}.1.2 255.255.255.252
     ip ospf network point-to-point
     ip ospf hello-interval 1
     ip ospf cost 10
    !
    interface GigabitEthernet0/1
     description LAN {{name}} to sw1 G0/2 !
    interface GigabitEthernet0/1.{{IT}}
     description PW IT {{name}} - {{to_name}}
     encapsulation dot1Q {{IT}}
     xconnect 10.10.{{to_id}}.1 {{id}}11 encapsulation mpls
     backup peer 10.10.{{to_id}}.2 {{id}}21
      backup delay 1 1
    !
    interface GigabitEthernet0/1.{{BS}}
     description PW BS {{name}} - {{to_name}}
     encapsulation dot1Q {{BS}}
     xconnect 10.10.{{to_id}}.1 {{to_id}}{{id}}11 encapsulation mpls
      backup peer 10.10.{{to_id}}.2 {{to_id}}{{id}}21
      backup delay 1 1
    !
    router ospf 10
     router-id 10.10.{{id}}.1
     auto-cost reference-bandwidth 10000
     network 10.0.0.0 0.255.255.255 area 0
     !

Файл с данными routers_info.yml

::

    - id: 11
      name: Liverpool
      to_name: LONDON
      IT: 791
      BS: 1550
      to_id: 1

    - id: 12
      name: Bristol
      to_name: LONDON
      IT: 793
      BS: 1510
      to_id: 1

    - id: 14
      name: Coventry
      to_name: Manchester
      IT: 892
      BS: 1650
      to_id: 2

Скрипт для генерации конфигураций router_config_generator_ver2.py

.. code:: python

    from jinja2 import Environment, FileSystemLoader
    import yaml

    env = Environment(loader=FileSystemLoader('templates'))
    template = env.get_template('router_template.txt')

    with open('routers_info.yml') as f:
        routers = yaml.safe_load(f)

    for router in routers:
        r1_conf = router['name']+'_r1.txt'
        with open(r1_conf, 'w') as f:
            f.write(template.render(router))

Файл router_config_generator.py импортирует из модуля jinja2: 

* **FileSystemLoader** - загрузчик, который позволяет работать с файловой системой 

  * тут указывается путь к каталогу, где находятся шаблоны 
  * в данном случае шаблон находится в каталоге templates 

* **Environment** - класс для описания параметров окружения. 
  В данном случае указан только загрузчик, но в нём можно указывать методы обработки шаблона

Обратите внимание, что шаблон теперь находится в каталоге **templates**.

