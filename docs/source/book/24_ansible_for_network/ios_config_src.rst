src
---

Параметр **src** позволяет указывать путь к файлу конфигурации или
шаблону конфигурации, которую нужно загрузить на устройство.

Этот параметр взаимоисключающий с lines (то есть, можно указывать или
lines, или src). Он заменяет модуль ios_template, который скоро будет
удален.

Конфигурация
~~~~~~~~~~~~

Пример playbook 11_ios_config_src.yml:

.. code:: yml

    ---

    - name: Run cfg commands on router
      hosts: 192.168.100.1

      tasks:

        - name: Config ACL
          ios_config:
            src: templates/acl_cfg.txt

В файле templates/acl_cfg.txt находится такая конфигурация:

::

    ip access-list extended IN_to_OUT
     permit tcp 10.0.1.0 0.0.0.255 any eq www
     permit tcp 10.0.1.0 0.0.0.255 any eq 22
     permit icmp any any
     deny   ip any any

Удаляем на маршрутизаторе этот ACL, если он остался с прошлых разделов,
и запускаем playbook:

::

    $ ansible-playbook 11_ios_config_src.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6j_ios_config_src.png

Теперь на маршрутизаторе настроен ACL:

::

    R1#sh run | s access
    ip access-list extended IN_to_OUT
     permit tcp 10.0.1.0 0.0.0.255 any eq www
     permit tcp 10.0.1.0 0.0.0.255 any eq 22
     permit icmp any any
     deny   ip any any

Если запустить playbook ещё раз, то никаких изменений не будет, так как
этот параметр также идемпотентен:

::

    $ ansible-playbook 11_ios_config_src.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6j_ios_config_src_2.png

Шаблон Jinja2
~~~~~~~~~~~~~

В параметре src можно указывать шаблон Jinja2.

Пример шаблона (файл templates/ospf.j2):

.. code:: j2

    router ospf 1
     router-id {{ mgmnt_ip }}
     ispf
     auto-cost reference-bandwidth 10000
    {% for ip in ospf_ints %}
     network {{ ip }} 0.0.0.0 area 0
    {% endfor %}

В шаблоне используются две переменные: 

* mgmnt_ip - IP-адрес, который будет использоваться как router-id 
* ospf_ints - список IP-адресов интерфейсов, на которых нужно включить OSPF

Для настройки OSPF на трёх маршрутизаторах нужно иметь возможность
использовать разные значения этих переменных для разных устройств. Для
таких задач используются файлы с переменными в каталоге host_vars.

В каталоге host_vars нужно создать такие файлы (если они ещё не
созданы):

Файл host_vars/192.168.100.1:

::

    ---

    hostname: london_r1
    mgmnt_loopback: 100
    mgmnt_ip: 10.0.0.1
    ospf_ints:
      - 192.168.100.1
      - 10.0.0.1
      - 10.255.1.1

Файл host_vars/192.168.100.2:

::

    ---

    hostname: london_r2
    mgmnt_loopback: 100
    mgmnt_ip: 10.0.0.2
    ospf_ints:
      - 192.168.100.2
      - 10.0.0.2
      - 10.255.2.2

Файл host_vars/192.168.100.3:

::

    ---

    hostname: london_r3
    mgmnt_loopback: 100
    mgmnt_ip: 10.0.0.3
    ospf_ints:
      - 192.168.100.3
      - 10.0.0.3
      - 10.255.3.3

Теперь можно создавать playbook 11_ios_config_src_jinja.yml:

.. code:: yml

    ---

    - name: Run cfg commands on router
      hosts: cisco-routers

      tasks:

        - name: Config OSPF
          ios_config:
            src: templates/ospf.j2

Так как Ansible сам найдет переменные в каталоге host_vars, их не нужно
указывать. Можно сразу запускать playbook:

::

    $ ansible-playbook 11_ios_config_src_jinja.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6j_ios_config_src_jinja.png

Теперь на всех маршрутизаторах настроен OSPF:

::

    R1#sh run | s ospf
    router ospf 1
     router-id 10.0.0.1
     ispf
     auto-cost reference-bandwidth 10000
     network 10.0.0.1 0.0.0.0 area 0
     network 10.255.1.1 0.0.0.0 area 0
     network 192.168.100.1 0.0.0.0 area 0

    R2#sh run | s ospf
    router ospf 1
     router-id 10.0.0.2
     ispf
     auto-cost reference-bandwidth 10000
     network 10.0.0.2 0.0.0.0 area 0
     network 10.255.2.2 0.0.0.0 area 0
     network 192.168.100.2 0.0.0.0 area 0

    router ospf 1
     router-id 10.0.0.3
     ispf
     auto-cost reference-bandwidth 10000
     network 10.0.0.3 0.0.0.0 area 0
     network 10.255.3.3 0.0.0.0 area 0
     network 192.168.100.3 0.0.0.0 area 0

Если запустить playbook ещё раз, то никаких изменений не будет:

::

    $ ansible-playbook 11_ios_config_src_jinja.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6j_ios_config_src_jinja_2.png

Совмещение с другими параметрами
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Параметр **src** совместим с такими параметрами: 

* backup 
* config 
* defaults 
* save
