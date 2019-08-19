after
-----

Параметр **after** указывает, какие команды выполнить после команд в
списке lines (или commands).

Команды, которые указаны в параметре after: 

* выполняются, только если должны быть внесены изменения. 
* при этом они будут выполнены
  независимо от того, есть они в конфигурации или нет.

Параметр after очень полезен в ситуациях, когда необходимо выполнить
команду, которая не сохраняется в конфигурации.

Например, команда no shutdown не сохраняется в конфигурации
маршрутизатора, и если добавить её в список lines, изменения будут
вноситься каждый раз при выполнении playbook.
Если написать команду no shutdown в списке after, она будет
применена только в том случае, если нужно вносить изменения (согласно
списка lines).

Пример использования параметра after в playbook
7_ios_config_after.yml:

::

    ---

    - name: Run cfg commands on router
      hosts: 192.168.100.1

      tasks:

        - name: Config interface
          ios_config:
            parents:
              - interface Ethernet0/3
            lines:
              - ip address 192.168.230.1 255.255.255.0
            after:
              - no shutdown

Первый запуск playbook, с внесением изменений:

::

    $ ansible-playbook 7_ios_config_after.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6f_ios_config_after.png

Второй запуск playbook (изменений нет, поэтому команда no shutdown не
выполняется):

::

    $ ansible-playbook 7_ios_config_after.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6f_ios_config_after_no_change.png

Рассмотрим ещё один пример использования after.

С помощью after можно сохранять конфигурацию устройства (playbook
7_ios_config_after_save.yml):

::

    ---

    - name: Run cfg commands on routers
      hosts: cisco-routers

      tasks:

        - name: Config line vty
          ios_config:
            parents:
              - line vty 0 4
            lines:
              - login local
              - transport input ssh
            after:
              - end
              - write

Результат выполнения playbook (изменения только на маршрутизаторе
192.168.100.1):

::

    $ ansible-playbook 7_ios_config_after_save.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6f_ios_config_after_save.png


