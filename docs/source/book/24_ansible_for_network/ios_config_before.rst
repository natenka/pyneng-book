before
------

Параметр **before** указывает, какие действия выполнить до команд в
списке lines.

Команды, которые указаны в параметре before: 
* выполняются, только если
должны быть внесены изменения. 
* при этом они будут выполнены
независимо от того, есть они в конфигурации или нет.

Параметр before полезен в ситуациях, когда какие-то действия необходимо
выполнить перед выполнением команд в списке lines.

При этом, как и after, параметр before не влияет на то, какие команды
сравниваются с конфигурацией. То есть, по-прежнему сравниваются только
команды в списке lines.

Playbook 8_ios_config_before.yml:

.. code:: yml

    ---

    - name: Run cfg commands on router
      hosts: 192.168.100.1

      tasks:

        - name: Config ACL
          ios_config:
            before:
              - no ip access-list extended IN_to_OUT
            parents:
              - ip access-list extended IN_to_OUT
            lines:
              - permit tcp 10.0.1.0 0.0.0.255 any eq www
              - permit tcp 10.0.1.0 0.0.0.255 any eq 22
              - permit icmp any any

В playbook 8_ios_config_before.yml ACL IN_to_OUT сначала удаляется
с помощью параметра before, а затем создается заново.

Таким образом, в ACL всегда находятся только те строки, которые заданы в
списке lines.

Запуск playbook с изменениями:

::

    $ ansible-playbook 8_ios_config_before.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6g_ios_config_before.png

Запуск playbook без изменений (команда в списке before не выполняется):

::

    $ ansible-playbook 8_ios_config_before.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6g_ios_config_before_no_updates.png
