backup
------

Параметр **backup** указывает, нужно ли делать резервную копию текущей
конфигурации устройства перед внесением изменений. Файл будет
копироваться в каталог backup относительно каталога, в котором находится
playbook (если каталог не существует, он будет создан).

Playbook 5_ios_config_backup.yml:

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
            backup: yes

Теперь каждый раз, когда выполняется playbook (даже если не нужно
вносить изменения в конфигурацию), в каталог backup будет копироваться
текущая конфигурация:

::

    $ ansible-playbook 5_ios_config_backup.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6d_ios_config_backup.png

В каталоге backup теперь находятся файлы такого вида (при каждом запуске
playbook они перезаписываются):

::

    192.168.100.1_config.2016-12-10@10:42:34
    192.168.100.2_config.2016-12-10@10:42:34
    192.168.100.3_config.2016-12-10@10:42:34

При работе с Python 3, может возникнуть ошибка "RuntimeError: dictionary
changed size during iteration". Ее можно исправить вручную. Для этого
надо запустить playbook с опцией -vvv и посмотреть где находится модуль
ios_config. В выводе также будет информация о том в какой строке
ошибка.

Пример ошибки:

::

    File "/home/vagrant/venv/py3_convert/lib/python3.6/site-packages/ansible/plugins/action/ios_config.py", line 57, in run
        for key in result.keys():
    RuntimeError: dictionary changed size during iteration

Для исправления, надо в указанной строке сменить
``for key in result.keys():`` на ``for key in list(result.keys()):``.
