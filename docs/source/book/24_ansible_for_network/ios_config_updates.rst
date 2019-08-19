Отображение обновлений
----------------------

В этом разделе рассматриваются варианты отображения информации об
обновлениях, которые выполнил модуль ios_config.

Playbook 2_ios_config_parents_basic.yml:

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

    Для того, чтобы playbook что-то менял, нужно сначала отменить
    команды - либо вручную, либо изменив playbook. Например, на
    маршрутизаторе 192.168.100.1 вместо строки transport input ssh
    вручную прописать строку transport input all.

Например, можно выполнить playbook с флагом verbose:

::

    $ ansible-playbook 2_ios_config_parents_basic.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6a_ios_config_parents_basic_verbose.png

В выводе в поле updates видно, какие именно команды Ansible отправил на
устройство. Изменения были выполнены только на маршрутизаторе
192.168.100.1.

Обратите внимание, что команда login local не отправлялась, так как она
настроена.
Поле updates в выводе есть только в том случае, когда есть
изменения.

В режиме verbose информация видна обо всех устройствах. Но было бы
удобней, чтобы информация отображалась только для тех устройств, для
которых произошли изменения.

Новый playbook 3_ios_config_debug.yml на основе
2_ios_config_parents_basic.yml:

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
          register: cfg

        - name: Show config updates
          debug: var=cfg.updates
          when: cfg.changed

Изменения в playbook: 

* результат работы первой задачи сохраняется в переменную **cfg**. 
* в следующей задаче модуль **debug** выводит содержимое поля **updates**. 
* но так как поле updates в выводе есть
  только в том случае, когда есть изменения, ставится условие when,
  которое проверяет, были ли изменения 
* задача будет выполняться, только если на устройстве были внесены изменения. 
* вместо ``when: cfg.changed`` можно написать ``when: cfg.changed == true``

Если запустить повторно playbook, когда изменений не было, задача Show
config updates пропускается:

::

    $ ansible-playbook 3_ios_config_debug.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6b_ios_config_debug_skipping.png

Если внести изменения в конфигурацию маршрутизатора 192.168.100.1
(изменить transport input ssh на transport input all):

::

    $ ansible-playbook 3_ios_config_debug.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6b_ios_config_debug_update.png

Теперь второе задание отображает информацию о том, какие именно
изменения были внесены на маршрутизаторе.
