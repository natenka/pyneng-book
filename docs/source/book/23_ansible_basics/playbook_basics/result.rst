Работа с результатами выполнения модуля
---------------------------------------

В этом разделе рассматриваются несколько способов, которые позволяют
посмотреть на вывод, полученный с устройств.

Примеры используют модуль raw, но аналогичные принципы работают и с
другими модулями.

verbose
~~~~~~~

В предыдущих разделах один из способов отобразить результат выполнения
команд уже использовался - флаг verbose.

Конечно, вывод не очень удобно читать, но, как минимум, он позволяет
увидеть, что команды выполнились. Также этот флаг позволяет подробно
посмотреть, какие шаги выполняет Ansible.

Пример запуска playbook с флагом verbose (вывод сокращен):

::

    ansible-playbook 1_show_commands_with_raw.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/playbook-verbose.png

При увеличении количества букв v в флаге, вывод становится более
подробным. Попробуйте вызывать этот же playbook и добавлять к флагу
буквы v (5 и больше показывают одинаковый вывод) таким образом:

::

    ansible-playbook 1_show_commands_with_raw.yml -vvv

В выводе видны результаты выполнения задачи, они возвращаются в формате
JSON: 

* **changed** - ключ, который указывает, были ли внесены изменения 
* **stdout** - вывод команды 
* **stdout_lines** - вывод в виде списка команд, разбитых построчно

register
~~~~~~~~

Параметр **register** сохраняет результат выполнения модуля в
переменную. Затем эта переменная может использоваться в шаблонах, в
принятии решений о ходе сценария или для отображения вывода.

Попробуем сохранить результат выполнения команды.

В playbook 2_register_vars.yml с помощью register вывод команды sh ip
int br сохранен в переменную sh_ip_int_br_result:

::

    ---

    - name: Run show commands on routers
      hosts: 192.168.100.1
      gather_facts: false

      tasks:

        - name: run sh ip int br
          ios_command:
            commands: sh ip int br
          register: sh_ip_int_br_result


Если запустить этот playbook, вывод не будет отличаться, так как вывод
только записан в переменную, но с переменной не выполняется никаких
действий. Следующий шаг - отобразить результат выполнения команды с
помощью модуля debug.

debug
~~~~~

Модуль debug позволяет отображать информацию на стандартный поток
вывода. Это может быть произвольная строка, переменная, факты об
устройстве.

Для отображения сохраненных результатов выполнения команды, в playbook
2_register_vars.yml добавлена задача с модулем debug:

::

    ---

    - name: Run show commands on routers
      hosts: 192.168.100.1
      gather_facts: false

      tasks:

        - name: run sh ip int br
          ios_command:
            commands: sh ip int br
          register: sh_ip_int_br_result

        - name: Debug registered var
          debug: var=sh_ip_int_br_result.stdout_lines

Обратите внимание, что выводится не всё содержимое переменной
sh_ip_int_br_result, а только содержимое stdout_lines. В
sh_ip_int_br_result.stdout_lines находится список строк, поэтому
вывод будет структурирован.

Результат запуска playbook выглядит так:

::

    $ ansible-playbook 2_register_vars.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/2_register_vars.png

register, debug, when
~~~~~~~~~~~~~~~~~~~~~

С помощью ключевого слова **when** можно указать условие, при выполнении
которого задача выполняется. Если условие не выполняется, то задача
пропускается.

.. note::

    when в Ansible используется, как if в Python.

Пример playbook 3_register_debug_when.yml:

::

    ---

    - name: Run show commands on routers
      hosts: 192.168.100.1
      gather_facts: false

      tasks:

        - name: run sh ip int br
          ios_command:
            commands: sh ip int br
          register: sh_ip_int_br_result

        - name: Debug registered var
          debug:
            msg: "IP адрес не найден"
          when: "'4.4.4.4' not in sh_ip_int_br_result.stdout[0]"


В последнем задании несколько изменений: 

* модуль debug отображает не содержимое сохраненной переменной, 
  а сообщение, которое указано в переменной msg. 
* условие when указывает, что данная задача выполнится
  только при выполнении условия 
* ``when: "'4.4.4.4' not in sh_ip_int_br_result.stdout[0]"`` - это условие
  означает, что задача будет выполнена только в том случае, если в выводе
  sh_ip_int_br_result.stdout будет найдена строка 4.4.4.4 


Выполнение playbook:

::

    $ ansible-playbook 3_register_debug_when.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/3_register_debug_when_skip.png

Обратите внимание на сообщения skipping - это означает, что задача не
выполнялась для указанных устройств. Не выполнилась она потому, что
условие в when не было выполнено.

Выполнение того же playbook, но после удаления адреса на устройстве:

::

    $ ansible-playbook 3_register_debug_when.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/3_register_debug_when.png

