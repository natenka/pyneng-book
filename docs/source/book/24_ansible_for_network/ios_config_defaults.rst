defaults
--------

Параметр **defaults** указывает, нужно ли собирать всю информацию с
устройства, в том числе и значения по умолчанию. Если включить этот
параметр, модуль будет собирать текущую конфигурацию с помощью команды
sh run all. По умолчанию этот параметр отключен, и конфигурация
проверяется командой sh run.

Этот параметр полезен в том случае, если в настройках указывается
команда, которая не видна в конфигурации. Например, такое может быть,
когда указан параметр, который и так используется по умолчанию.

Если не использовать параметр defaults и указать команду, которая
настроена по умолчанию, то при каждом запуске playbook будут вноситься
изменения.

Происходит это потому, что Ansible каждый раз вначале проверяет наличие
команд в соответствующем режиме. Если команд нет, то соответствующая
задача выполняется.

Например, в таком playbook каждый раз будут вноситься изменения
(попробуйте запустить его самостоятельно):

.. code:: yml

    ---

    - name: Run cfg commands on routers
      hosts: cisco-routers

      tasks:

        - name: Config interface
          ios_config:
            parents:
              - interface Ethernet0/2
            lines:
              - ip address 192.168.200.1 255.255.255.0
              - ip mtu 1500

Если добавить параметр ``defaults: yes``, изменения уже не будут
внесены, если не хватало только команды ip mtu 1500 (playbook
6_ios_config_defaults.yml):

::

    ---

    - name: Run cfg commands on routers
      hosts: cisco-routers

      tasks:

        - name: Config interface
          ios_config:
            parents:
              - interface Ethernet0/2
            lines:
              - ip address 192.168.200.1 255.255.255.0
              - ip mtu 1500
            defaults: yes

Запуск playbook:

::

    $ ansible-playbook 6_ios_config_defaults.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6e_ios_config_defaults.png


