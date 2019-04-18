Модуль ios\_command
-------------------

Модуль **ios\_command** отправляет команду show на устройство под
управлением IOS и возвращает результат выполнения команды.

    Модуль ios\_command не поддерживает отправку команд в
    конфигурационном режиме. Для этого используется отдельный модуль -
    ios\_config.

Модуль ios\_command поддерживает такие параметры: \* **commands** -
список команд, которые надо отправить на устройство \* **wait\_for**
(или waitfor) - список условий, на которые надо проверить вывод команды.
Задача ожидает выполнения всех условий. Если после указанного количества
попыток выполнения команды условия не выполняются, будет считаться, что
задача выполнена неудачно. \* **match** - этот параметр используется
вместе с wait\_for для указания политики совпадения. Если параметр match
установлен в all, должны выполниться все условия в wait\_for. Если
параметр равен any, достаточно, чтобы выполнилось одно из условий. \*
**retries** - указывает количество попыток выполнения команды, прежде
чем она будет считаться невыполненной. По умолчанию - 10 попыток. \*
**interval** - интервал в секундах между повторными попытками выполнить
команду. По умолчанию - 1 секунда.

Перед отправкой самой команды модуль: \* выполняет аутентификацию по SSH
\* переходит в режим enable \* выполняет команду ``terminal length 0``,
чтобы вывод команд show отражался полностью, а не постранично \*
выполняет команду ``terminal width 512``

Пример использования модуля ios\_command (playbook 1\_ios\_command.yml):

::

    ---

    - name: Run show commands on routers
      hosts: cisco-routers

      tasks:

        - name: run sh ip int br
          ios_command:
            commands: show ip int br
          register: sh_ip_int_br_result

        - name: Debug registered var
          debug: var=sh_ip_int_br_result.stdout_lines

Модуль ios\_command ожидает, как минимум, такие аргументы: \* commands -
список команд, которые нужно отправить на устройство

    Обратите внимание, что параметр register находится на одном уровне с
    именем задачи и модулем, а не на уровне параметров модуля
    ios\_command.

Результат выполнения playbook:

::

    $ ansible-playbook 1_ios_command.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/2_ios_command.png
   :alt: ios\_command

   ios\_command
    В отличие от использования модуля raw, playbook не указывает, что
    были выполнены изменения.

Выполнение нескольких команд
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Модуль ios\_command позволяет выполнять несколько команд.

Playbook 2\_ios\_command.yml выполняет несколько команд и получает их
вывод:

::

    ---

    - name: Run show commands on routers
      hosts: cisco-routers

      tasks:

        - name: run show commands
          ios_command:
            commands:
              - show ip int br
              - sh ip route
          register: show_result

        - name: Debug registered var
          debug: var=show_result.stdout_lines

В первой задаче указываются две команды, поэтому синтаксис должен быть
немного другим - команды должны быть указаны как список, в формате YAML.

Результат выполнения playbook (вывод сокращен):

::

    $ ansible-playbook 2_ios_command.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/2a_ios_command.png
   :alt: ios\_command

   ios\_command
Обе команды выполнились на всех устройствах.

Если модулю передаются несколько команд, результат выполнения команд
находится в переменных stdout и stdout\_lines в списке. Вывод будет в
том порядке, в котором команды описаны в задаче.

За счет этого, например, можно вывести результат выполнения первой
команды, указав:

::

        - name: Debug registered var
          debug: var=show_result.stdout_lines[0]

Обработка ошибок
~~~~~~~~~~~~~~~~

В модуле встроено распознание ошибок. Поэтому, если команда выполнена с
ошибкой, модуль отобразит, что возникла ошибка.

Например, если сделать ошибку в команде и запустить playbook еще раз

::

    $ ansible-playbook 2_ios_command.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/2_ios_command-fail.png
   :alt: ios\_command

   ios\_command
Ansible обнаружил ошибку и возвращает сообщение ошибки. В данном случае
- 'Invalid input'.

Аналогичным образом модуль обнаруживает ошибки: \* Ambiguous command \*
Incomplete command

wait\_for
~~~~~~~~~

Параметр wait\_for (или waitfor) позволяет указывать список условий, на
которые надо проверить вывод команды.

Пример playbook (файл 3\_ios\_command\_wait\_for.yml):

.. code:: yml

    ---

    - name: Run show commands on routers
      hosts: cisco-routers

      tasks:

        - name: run show commands
          ios_command:
            commands: ping 192.168.100.100
            wait_for:
              - result[0] contains 'Success rate is 100 percent'

В playbook всего одна задача, которая отправляет команду ping
192.168.100.100, и проверяет, есть ли в выводе команды фраза 'Success
rate is 100 percent'.

Если в выводе команды содержится эта фраза, задача считается корректно
выполненной.

Запуск playbook:

::

    $ ansible-playbook 3_ios_command_wait_for.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/3_ios_command_waitfor.png
   :alt: ios\_command

   ios\_command
Если указан IP-адрес, который не доступен, результат будет таким:

::

    $ ansible-playbook 3_ios_command_wait_for.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/3_ios_command_waitfor_timeout.png
   :alt: ios\_command

   ios\_command
Такой вывод из-за того, что по умолчанию таймаут для каждого пакета 2
секунды, и за время выполнения playbook команда еще не выполнена.

По умолчанию есть 10 попыток выполнить команду, при этом между каждыми
двумя попытками интервал - секунда. В реальной ситуации при проверке
доступности адреса лучше сделать хотя бы две попытки.

Playbook 3\_ios\_command\_wait\_for\_interval.yml выполняет две попытки,
на каждую попытку 12 секунд:

::

    ---

    - name: Run show commands on routers
      hosts: cisco-routers

      tasks:

        - name: run show commands
          ios_command:
            commands: ping 192.168.100.5 timeout 1
            wait_for:
              - result[0] contains 'Success rate is 100 percent'
            retries:  2
            interval: 12

В этом случае вывод будет таким:

::

    $ ansible-playbook 3_ios_command_wait_for_interval.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/3_ios_command_waitfor_fail.png
   :alt: ios\_command

   ios\_command

