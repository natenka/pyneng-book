save\_when
----------

Параметр **save\_when** позволяет указать, нужно ли сохранять текущую
конфигурацию в стартовую.

Доступные варианты значений: \* always - всегда сохранять конфигурацию
(в этом случае флаг modified будет равен True) \* never (по умолчанию) -
не сохранять конфигурацию \* modified - в этом случае конфигурация
сохраняется только при наличии изменений

К сожалению, на данный момент (версия ansible 2.4) этот параметр не
отрабатывает корректно, так как на устройство отправляется команда copy
running-config startup-config, но при этом не отправляется подтверждение
на сохранение. Из-за этого при запуске playbook с параметром save\_when,
выставленным в always или modified, появляется такая ошибка:

::

    fatal: [192.168.100.2]: FAILED! => {"changed": false, "failed": true,
    "msg": "timeout trying to send command: b'copy running-config startup-config'", "rc": 1}

Исправить это достаточно легко, настроив в IOS:

::

    file prompt quiet

    По умолчанию настроено ``file prompt alert``

Если такая настройка не подходит на постоянной основе, можно ее
настраивать до и исправлять после использования Ansible.

Еще один вариант - самостоятельно сделать сохранение, используя модуль
ios\_command.

Playbook 4\_ios\_config\_save\_when.yml:

.. code:: yml

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
            #save_when: modified
          register: cfg


        - name: Save config
          ios_command:
            commands:
              - write
          when: cfg.changed

    Надо внести изменения на маршрутизаторе 192.168.100.1. Например,
    изменить строку transport input all на transport input ssh.

Выполнение playbook:

::

    $ ansible-playbook 4_ios_config_save_when.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6c_ios_config_save_2.png
   :alt: 6c\_ios\_config\_save

   6c\_ios\_config\_save

