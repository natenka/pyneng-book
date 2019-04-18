Модуль ios\_facts
-----------------

Модуль ios\_facts собирает информацию с устройств под управлением IOS.

Информация берется из таких команд: \* dir \* show version \* show
memory statistics \* show interfaces \* show ipv6 interface \* show lldp
\* show lldp neighbors detail \* show running-config

    Чтобы видеть, какие команды Ansible выполняет на оборудовании, можно
    настроить `EEM
    applet <http://xgu.ru/wiki/EEM#.D0.9F.D1.80.D0.B8.D0.BC.D0.B5.D1.80.D1.8B_.D1.81.D0.BE.D0.B1.D1.8B.D1.82.D0.B8.D1.8F_cli>`__,
    который будет генерировать лог сообщения о выполненных командах.

В модуле можно указывать, какие параметры собирать - можно собирать всю
информацию, а можно только подмножество. По умолчанию модуль собирает
всю информацию, кроме конфигурационного файла.

Какую информацию собирать, указывается в параметре **gather\_subset**.
Поддерживаются такие варианты (указаны также команды, которые будут
выполняться на устройстве): \* **all** \* **hardware** \* dir \* show
version \* show memory statistics \* **config** \* show version \* show
running-config \* **interfaces** \* dir \* show version \* show
interfaces \* show ip interface \* show ipv6 interface \* show lldp \*
show lldp neighbors detail

Собрать все факты:

::

    - ios_facts:
        gather_subset: all

Собрать только подмножество interfaces:

::

    - ios_facts:
        gather_subset:
          - interfaces

Собрать всё, кроме hardware:

::

    - ios_facts:
        gather_subset:
          - "!hardware"

Ansible собирает такие факты: \* ansible\_net\_all\_ipv4\_addresses -
список IPv4 адресов на устройстве \* ansible\_net\_all\_ipv6\_addresses
- список IPv6 адресов на устройстве \* ansible\_net\_config -
конфигурация (для Cisco sh run) \* ansible\_net\_filesystems - файловая
система устройства \* ansible\_net\_gather\_subset - какая информация
собирается (hardware, default, interfaces, config) \*
ansible\_net\_hostname - имя устройства \* ansible\_net\_image - имя и
путь ОС \* ansible\_net\_interfaces - словарь со всеми интерфейсами
устройства. Имена интерфейсов - ключи, а данные - параметры каждого
интерфейса \* ansible\_net\_memfree\_mb - сколько свободной памяти на
устройстве \* ansible\_net\_memtotal\_mb - сколько памяти на устройстве
\* ansible\_net\_model - модель устройства \* ansible\_net\_serialnum -
серийный номер \* ansible\_net\_version - версия IOS

Использование модуля
~~~~~~~~~~~~~~~~~~~~

Пример playbook 1\_ios\_facts.yml с использованием модуля ios\_facts
(собираются все факты):

::

    ---

    - name: Collect IOS facts
      hosts: cisco-routers

      tasks:

        - name: Facts
          ios_facts:
            gather_subset: all

::

    $ ansible-playbook 1_ios_facts.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/5_ios_facts.png
   :alt: 5\_ios\_facts

   5\_ios\_facts
Для того, чтобы посмотреть, какие именно факты собираются с устройства,
можно добавить флаг -v (информация сокращена):

::

    $ ansible-playbook 1_ios_facts.yml -v
    Using /home/nata/pyneng_course/chapter15/ansible.cfg as config file

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/5_ios_facts_verbose.png
   :alt: 5\_ios\_facts

   5\_ios\_facts
После того, как Ansible собрал факты с устройства, все факты доступны
как переменные в playbook, шаблонах и т.д.

Например, можно отобразить содержимое факта с помощью debug (playbook
2\_ios\_facts\_debug.yml):

.. code:: yml

    ---

    - name: Collect IOS facts
      hosts: 192.168.100.1

      tasks:

        - name: Facts
          ios_facts:
            gather_subset: all

        - name: Show ansible_net_all_ipv4_addresses fact
          debug: var=ansible_net_all_ipv4_addresses

        - name: Show ansible_net_interfaces fact
          debug: var=ansible_net_interfaces['Ethernet0/0']

Результат выполнения playbook:

::

    $ ansible-playbook 2_ios_facts_debug.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/5_ios_facts_debug.png
   :alt: 5\_ios\_facts\_debug

   5\_ios\_facts\_debug
Сохранение фактов
~~~~~~~~~~~~~~~~~

В том виде, в котором информация отображается в режиме verbose, довольно
сложно понять какая информация собирается об устройствах. Для того,
чтобы лучше понять, какая информация собирается об устройствах и в каком
формате, скопируем полученную информацию в файл.

Для этого будет использоваться модуль copy.

Playbook 3\_ios\_facts.yml собирает всю информацию об устройствах и
записывает в разные файлы (создайте каталог all\_facts перед запуском
playbook или раскомментируйте задачу Create all\_facts dir, и Ansible
создаст каталог сам):

::

    ---

    - name: Collect IOS facts
      hosts: cisco-routers

      tasks:

        - name: Facts
          ios_facts:
            gather_subset: all
          register: ios_facts_result

        #- name: Create all_facts dir
        #  file:
        #    path: ./all_facts/
        #    state: directory
        #    mode: 0755

        - name: Copy facts to files
          copy:
            content: "{{ ios_facts_result | to_nice_json }}"
            dest: "all_facts/{{inventory_hostname}}_facts.json"

Модуль copy позволяет копировать файлы с управляющего хоста (на котором
установлен Ansible) на удаленный хост. Но так как в этом случае, указан
параметр ``connection: local``, файлы будут скопированы на локальный
хост.

Чаще всего, модуль copy используется таким образом:

::

    - copy:
        src: /srv/myfiles/foo.conf
        dest: /etc/foo.conf

Но в данном случае нет исходного файла, содержимое которого нужно
скопировать. Вместо этого, есть содержимое переменной
ios\_facts\_result, которое нужно перенести в файл
all\_facts/{{inventory\_hostname}}\_facts.json.

Для того, чтобы перенести содержимое переменной в файл, в модуле copy
вместо src используется параметр content.

В строке ``content: "{{ ios_facts_result | to_nice_json }}"`` \*
параметр to\_nice\_json - это фильтр Jinja2, который преобразует
информацию переменной в формат, в котором удобней читать информацию \*
переменная в формате Jinja2 должна быть заключена в двойные фигурные
скобки, а также указана в двойных кавычках

Так как в пути dest используются имена устройств, будут сгенерированы
уникальные файлы для каждого устройства.

Результат выполнения playbook:

::

    $ ansible-playbook 3_ios_facts.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/5a_ios_facts.png
   :alt: 5a\_ios\_facts

   5a\_ios\_facts
После этого в каталоге all\_facts находятся такие файлы:

::

    192.168.100.1_facts.json
    192.168.100.2_facts.json
    192.168.100.3_facts.json

Содержимое файла all\_facts/192.168.100.1\_facts.json:

::

    {
        "ansible_facts": {
            "ansible_net_all_ipv4_addresses": [
                "192.168.200.1",
                "192.168.100.1",
                "10.1.1.1"
            ],
            "ansible_net_all_ipv6_addresses": [],
            "ansible_net_config": "Building configuration...\n\nCurrent configuration :
    ...

Сохранение информации об устройствах не только поможет разобраться,
какая информация собирается, но и может быть полезным для дальнейшего
использования информации. Например, можно использовать факты об
устройстве в шаблоне.

При повторном выполнении playbook Ansible не будет изменять информацию в
файлах, если факты об устройстве не изменились

Если информация изменилась, для соответствующего устройства будет
выставлен статус changed. Таким образом, по выполнению playbook всегда
понятно, когда какая-то информация изменилась.

Повторный запуск playbook (без изменений):

::

    $ ansible-playbook 3_ios_facts.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/5a_ios_facts_no_change.png
   :alt: 5a\_ios\_facts

   5a\_ios\_facts

