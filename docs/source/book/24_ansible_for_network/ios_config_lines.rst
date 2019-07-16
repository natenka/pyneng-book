lines (commands)
----------------

Самый простой способ использовать модуль ios_config - отправлять
команды глобального конфигурационного режима с параметром lines.
Для параметра lines есть alias commands, то есть, можно вместо lines
писать commands.

Пример playbook 1_ios_config_lines.yml:

.. code:: yml

    ---

    - name: Run cfg commands on routers
      hosts: cisco-routers

      tasks:

        - name: Config password encryption
          ios_config:
            lines:
              - service password-encryption

Используется переменная cli, которая указана в файле
group_vars/all.yml.

Результат выполнения playbook:

::

    $ ansible-playbook 1_ios_config_lines.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6_ios_config_lines.png

Ansible выполняет такие команды: 

* terminal length 0 
* enable 
* show running-config - чтобы проверить, есть ли эта команда на устройстве.
  Если команда есть, задача выполняться не будет. Если команды нет, задача
  выполнится 
* если команды, которая указана в задаче, нет в конфигурации: 

  * configure terminal 
  * service password-encryption 
  * end

Так как модуль каждый раз проверяет конфигурацию, прежде чем применит
команду, модуль идемпотентен. То есть, если ещё раз запустить playbook,
изменения не будут выполнены:

::

    $ ansible-playbook 1_ios_config_lines.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6_ios_config_lines_2.png

.. warning::

    Обязательно пишите команды полностью, а не сокращенно. И обращайте
    внимание, что для некоторых команд IOS сам добавляет параметры. Если
    писать команду не в том виде, в котором она реально видна в
    конфигурационном файле, модуль не будет идемпотентен. Он будет всё
    время считать, что команды нет, и вносить изменения каждый раз.

Параметр lines позволяет отправлять и несколько команд (playbook
1_ios_config_mult_lines.yml):

::

    ---

    - name: Run cfg commands on routers
      hosts: cisco-routers

      tasks:

        - name: Send config commands
          ios_config:
            lines:
              - service password-encryption
              - no ip http server
              - no ip http secure-server
              - no ip domain lookup

Результат выполнения:

::

    $ ansible-playbook 1_ios_config_mult_lines.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6_ios_config_mult_lines.png


