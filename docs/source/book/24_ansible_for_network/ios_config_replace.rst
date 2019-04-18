replace
-------

Параметр replace указывает, как именно нужно заменять конфигурацию: \*
**line** - в этом режиме отправляются только те команды, которых нет в
конфигурации. Этот режим используется по умолчанию \* **block** - в этом
режиме отправляются все команды, если хотя бы одной команды нет

replace: line
~~~~~~~~~~~~~

Режим ``replace: line`` - это режим работы по умолчанию. В этом режиме,
если были обнаружены изменения, отправляются только недостающие строки.

Например, на маршрутизаторе такой ACL:

::

    R1#sh run | s access
    ip access-list extended IN_to_OUT
     permit tcp 10.0.1.0 0.0.0.255 any eq www
     permit tcp 10.0.1.0 0.0.0.255 any eq 22
     permit icmp any any

Попробуем запустить такой playbook 10\_ios\_config\_replace\_line.yml:

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
              - deny   ip any any

Выполнение playbook:

::

    $ ansible-playbook 10_ios_config_replace_line.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6i_ios_config_replace_line.png
   :alt: 6i\_ios\_config\_replace\_line

   6i\_ios\_config\_replace\_line
После этого на маршрутизаторе такой ACL:

::

    R1#sh run | s access
    ip access-list extended IN_to_OUT
     deny   ip any any

В данном случае модуль проверил, каких команд не хватает в ACL (так как
режим по умолчанию match: line), обнаружил, что не хватает команды
``deny ip any any``, и добавил её. Но, так как ACL сначала удаляется, а
затем применяется список команд lines, получилось, что у нас теперь ACL
с одной строкой.

В таких ситуациях подходит режим ``replace: block``.

replace: block
~~~~~~~~~~~~~~

В режиме ``replace: block`` отправляются все команды из списка lines (и
parents), если на устройстве нет хотя бы одной из этих команд.

Повторим предыдущий пример.

ACL на маршрутизаторе:

::

    R1#sh run | s access
    ip access-list extended IN_to_OUT
     permit tcp 10.0.1.0 0.0.0.255 any eq www
     permit tcp 10.0.1.0 0.0.0.255 any eq 22
     permit icmp any any

Playbook 10\_ios\_config\_replace\_block.yml:

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
              - deny   ip any any
            replace: block

Выполнение playbook:

::

    $ ansible-playbook 10_ios_config_replace_block.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/6i_ios_config_replace_block.png
   :alt: 6i\_ios\_config\_replace\_block

   6i\_ios\_config\_replace\_block
В результате на маршрутизаторе такой ACL:

::

    R1#sh run | s access
    ip access-list extended IN_to_OUT
     permit tcp 10.0.1.0 0.0.0.255 any eq www
     permit tcp 10.0.1.0 0.0.0.255 any eq 22
     permit icmp any any
     deny   ip any any

