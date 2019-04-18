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
   :alt: Verbose playbook

   Verbose playbook
При увеличении количества букв v в флаге, вывод становится более
подробным. Попробуйте вызывать этот же playbook и добавлять к флагу
буквы v (5 и больше показывают одинаковый вывод) таким образом:

::

    ansible-playbook 1_show_commands_with_raw.yml -vvv

В выводе видны результаты выполнения задачи, они возвращаются в формате
JSON: \* **changed** - ключ, который указывает, были ли внесены
изменения \* **rc** - return code. Это поле появляется в выводе тех
модулей, которые выполняют какие-то команды \* **stderr** - ошибки при
выполнении команды. Это поле появляется в выводе тех модулей, которые
выполняют какие-то команды \* **stdout** - вывод команды \*
**stdout\_lines** - вывод в виде списка команд, разбитых построчно

register
~~~~~~~~

Параметр **register** сохраняет результат выполнения модуля в
переменную. Затем эта переменная может использоваться в шаблонах, в
принятии решений о ходе сценария или для отображения вывода.

Попробуем сохранить результат выполнения команды.

В playbook 2\_register\_vars.yml с помощью register вывод команды sh ip
int br сохранен в переменную sh\_ip\_int\_br\_result:

::

    ---

    - name: Run show commands on routers
      hosts: cisco-routers
      gather_facts: false

      tasks:

        - name: run sh ip int br
          raw: sh ip int br | ex unass
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
2\_register\_vars.yml добавлена задача с модулем debug:

::

    ---

    - name: Run show commands on routers
      hosts: cisco-routers
      gather_facts: false

      tasks:

        - name: run sh ip int br
          raw: sh ip int br | ex unass
          register: sh_ip_int_br_result

        - name: Debug registered var
          debug: var=sh_ip_int_br_result.stdout_lines

Обратите внимание, что выводится не всё содержимое переменной
sh\_ip\_int\_br\_result, а только содержимое stdout\_lines. В
sh\_ip\_int\_br\_result.stdout\_lines находится список строк, поэтому
вывод будет структурирован.

Результат запуска playbook выглядит так:

::

    $ ansible-playbook 2_register_vars.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/2_register_vars.png
   :alt: Verbose playbook

   Verbose playbook
register, debug, when
~~~~~~~~~~~~~~~~~~~~~

С помощью ключевого слова **when** можно указать условие, при выполнении
которого задача выполняется. Если условие не выполняется, то задача
пропускается.

    when в Ansible используется, как if в Python.

Пример playbook 3\_register\_debug\_when.yml:

::

    ---

    - name: Run show commands on routers
      hosts: cisco-routers
      gather_facts: false

      tasks:

        - name: run sh ip int br
          raw: sh ip int bri | ex unass
          register: sh_ip_int_br_result

        - name: Debug registered var
          debug:
            msg: "Error in command"
          when: "'invalid' in sh_ip_int_br_result.stdout"

В последнем задании несколько изменений: \* модуль debug отображает не
содержимое сохраненной переменной, а сообщение, которое указано в
переменной msg. \* условие when указывает, что данная задача выполнится
только при выполнении условия \*
``when: "'invalid' in sh_ip_int_br_result.stdout"`` - это условие
означает, что задача будет выполнена только в том случае, если в выводе
sh\_ip\_int\_br\_result.stdout будет найдена строка invalid (например,
когда неправильно введена команда)

    Модули, которые работают с сетевым оборудованием, автоматически
    проверяют ошибки при выполнении команд. Тут этот пример используется
    для демонстрации возможностей Ansible.

Выполнение playbook:

::

    $ ansible-playbook 3_register_debug_when.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/3_register_debug_when_skip.png
   :alt: Verbose playbook

   Verbose playbook
Обратите внимание на сообщения skipping - это означает, что задача не
выполнялась для указанных устройств. Не выполнилась она потому, что
условие в when не было выполнено.

Выполнение того же playbook, но с ошибкой в команде:

::

    ---

    - name: Run show commands on routers
      hosts: cisco-routers
      gather_facts: false

      tasks:

        - name: run sh ip int br
          raw: shh ip int bri | ex unass
          register: sh_ip_int_br_result

        - name: Debug registered var
          debug:
            msg: "Error in command"
          when: "'invalid' in sh_ip_int_br_result.stdout"

Теперь результат выполнения такой:

::

    $ ansible-playbook 3_register_debug_when.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/3_register_debug_when.png
   :alt: Verbose playbook

   Verbose playbook
Так как команда была с ошибкой, сработало условие, которое описано в
when, и задача вывела сообщение с помощью модуля debug.
