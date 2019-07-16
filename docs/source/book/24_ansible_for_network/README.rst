Особенности подключения к сетевому оборудованию
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

При работе с сетевым оборудованием надо указать, что должно
использоваться подключение типа network_cli. Это можно указывать в
инвентарном файле, файлах с перемеными и т.д.

Пример настройки для сценария (play):

::

    ---

    - name: Run show commands on routers
      hosts: cisco-routers
      connection: network_cli

В Ansible переменные можно указывать в разных местах, поэтому те же
настройки можно указать по-другому.

Например, в инвентарном файле:

::

    [cisco-routers]
    192.168.100.1
    192.168.100.2
    192.168.100.3

    [cisco-switches]
    192.168.100.100

    [cisco-routers:vars]
    ansible_connection=network_cli

Или в файлах переменных, например, в group_vars/all.yml:

::

    ---

    ansible_connection: network_cli

Модули, которые используются для работы с сетевым оборудованием, требуют
задания нескольких параметров.

    Все описание и примеры относятся к модулям ios_x и могут отличаться
    для других модулей.

Для каждой задачи должны быть доступны такие параметры: 

* ansible_network_os - например, ios, eos 
* ansible_user - имя пользователя 
* ansible_password - пароль 
* ansible_become - нужно ли переходить в привилегированный режим (enable, для Cisco) 
* ansible_become_method - каким образом надод переходить в
  привилегированный режим 
* ansible_become_pass - пароль для привилегированного режима

Пример указания всех параметров в group_vars/all.yml:

::

    ---

    ansible_connection: network_cli
    ansible_network_os: ios
    ansible_user: cisco
    ansible_password: cisco
    ansible_become: yes
    ansible_become_method: enable
    ansible_become_pass: cisco

Подготовка к работе с сетевыми модулями
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

В следующих разделах рассматривается работа с модулями ios_command,
ios_facts и ios_config. Для того, чтобы все примеры playbook работали,
надо создать несколько файлов (проверить, что они есть).

Инвентарный файл myhosts:

::

    [cisco-routers]
    192.168.100.1
    192.168.100.2
    192.168.100.3

    [cisco-switches]
    192.168.100.100

Конфигурационный файл ansible.cfg:

::

    [defaults]

    inventory = ./myhosts

В файле group_vars/all.yml надо создать параметры для подключения к
оборудованию:

::

    ---

    ansible_connection: network_cli
    ansible_network_os: ios
    ansible_user: cisco
    ansible_password: cisco
    ansible_become: yes
    ansible_become_method: enable
    ansible_become_pass: cisco

