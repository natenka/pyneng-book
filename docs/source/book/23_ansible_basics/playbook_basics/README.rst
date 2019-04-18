Основы playbooks
================

Playbook (файл сценариев) — это файл, в котором описываются действия,
которые нужно выполнить на какой-то группе хостов.

Внутри playbook: \* play - это набор задач, которые нужно выполнить для
группы хостов \* task - это конкретная задача. В задаче есть как
минимум: \* описание (название задачи можно не писать, но очень
рекомендуется) \* модуль и команда (действие в модуле)

Синтаксис playbook
------------------

Playbook описываются в формате YAML.

{% if book.book\_name == "ansible\_neteng" %} > Синтаксис YAML описан в
`разделе YAML курса "Python для сетевых
инженеров" <https://natenka.gitbooks.io/pyneng/content/book/17_serialization/3_yaml.html>`__
или в `документации
Ansible <http://docs.ansible.com/ansible/YAMLSyntax.html>`__. {% else %}
> Синтаксис YAML описан в `разделе
YAML <../../17_serialization/3_yaml.md>`__ или в `документации
Ansible <http://docs.ansible.com/ansible/YAMLSyntax.html>`__. {% endif
%}

Пример синтаксиса playbook
~~~~~~~~~~~~~~~~~~~~~~~~~~

    Все примеры этого раздела находятся в каталоге 2\_playbook\_basics

Пример plabook 1\_show\_commands\_with\_raw.yml:

::

    ---

    - name: Run show commands on routers
      hosts: cisco-routers
      gather_facts: false

      tasks:

        - name: run sh ip int br        
          raw: sh ip int br | ex unass

        - name: run sh ip route
          raw: sh ip route


    - name: Run show commands on switches
      hosts: cisco-switches
      gather_facts: false

      tasks:

        - name: run sh int status
          raw: sh int status

        - name: run sh vlan
          raw: show vlan

В playbook два сценария (play): \*
``name: Run show commands on routers`` - имя сценария (play). Этот
параметр обязательно должен быть в любом сценарии \*
``hosts: cisco-routers`` - сценарий будет применяться к устройствам в
группе cisco-routers \* тут может быть указано и несколько групп,
например, таким образом: ``hosts: cisco-routers:cisco-switches``.
Подробнее в
`документации <http://docs.ansible.com/ansible/intro_patterns.html>`__
\* обычно, в play надо указывать параметр **remote\_user**. Но, так как
мы указали его в конфигурационном файле Ansible, можно не указывать его
в play. \* ``gather_facts: false`` - отключение сбора фактов об
устройстве, так как для сетевого оборудования надо использовать
отдельные модули для сбора фактов. \* в разделе `конфигурационный
файл <../1_ansible_basics/configuration..md>`__ рассматривалось, как
отключить сбор фактов по умолчанию. Если он отключен, то параметр
gather\_facts в play не нужно указывать. \* ``tasks:`` - дальше идет
перечень задач \* в каждой задаче настроено имя (опционально) и
действие. Действие может быть только одно. \* в действии указывается,
какой модуль использовать, и параметры модуля.

И тот же playbook с отображением элементов:

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/playbook.png
   :alt: Ansible playbook

   Ansible playbook
Так выглядит выполнение playbook:

::

    $ ansible-playbook 1_show_commands_with_raw.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/playbook_execution.png
   :alt: Ansible playbook

   Ansible playbook
    Обратите внимание, что для запуска playbook используется другая
    команда. Для ad-hoc команды использовалась команда ansible. А для
    playbook - ansible-playbook.

Для того, чтобы убедиться, что команды, которые указаны в задачах,
выполнились на устройствах, запустите playbook с опцией -v (вывод
сокращен):

::

    $ ansible-playbook 1_show_commands_with_raw.yml -v

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/playbook-verbose.png
   :alt: Verbose playbook

   Verbose playbook
В следующих разделах мы научимся отображать эти данные в нормальном
формате и посмотрим, что с ними можно делать.

Порядок выполнения задач и сценариев
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Сценарии (play) и задачи (task) выполняются последовательно, в том
порядке, в котором они описаны в playbook.

Если в сценарии, например, две задачи, то сначала первая задача должна
быть выполнена для всех устройств, которые указаны в параметре hosts.
Только после того, как первая задача была выполнена для всех хостов,
начинается выполнение второй задачи.

Если в ходе выполнения playbook возникла ошибка в задаче на каком-то
устройстве, это устройство исключается, и другие задачи на нём
выполняться не будут.

Например, заменим пароль пользователя cisco на cisco123 (правильный
cisco) на маршрутизаторе 192.168.100.1 и запустим playbook заново:

::

    $ ansible-playbook 1_show_commands_with_raw.yml

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/playbook_failed_execution.png
   :alt: Ansible playbook

   Ansible playbook
Обратите внимание на ошибку в выполнении первой задачи для
маршрутизатора 192.168.100.1.

Во второй задаче 'TASK [run sh ip route]', Ansible уже исключил
маршрутизатор и выполняет задачу только для маршрутизаторов
192.168.100.2 и 192.168.100.3.

Еще один важный аспект - Ansible выдал сообщение:

::

    to retry, use: --limit @/home/vagrant/repos/pyneng-examples-exercises/examples/23_ansible/2_playbook_basics/1_show_commands_with_raw.retry

Если при выполнении playbook, на каком-то устройстве возникла ошибка,
Ansible создает специальный файл, который называется точно так же, как
playbook, но расширение меняется на retry. (Если Вы выполняете задания
параллельно, то этот файл должен появиться у Вас)

В этом файле хранится имя или адрес устройства, на котором возникла
ошибка. Так выглядит файл 1\_show\_commands\_with\_raw.retry сейчас:

::

    192.168.100.1

Создается этот файл для того, чтобы можно было перезапустить playbook
заново только для проблемного устройства (устройств). То есть, надо
исправить проблему с устройством и заново запустить playbook.

Настраиваем правильный пароль на маршрутизаторе 192.168.100.1, а затем
перезапускаем playbook таким образом:

::

    $ ansible-playbook 1_show_commands_with_raw.yml --limit @/home/vagrant/repos/pyneng-examples-exercises/examples/23_ansible_basics/2_playbook_basics/1_show_commands_with_raw.retry

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/15_ansible/playbook-retry.png
   :alt: Ansible playbook

   Ansible playbook
Ansible взял список устройств, которые перечислены в файле retry, и
выполнил playbook только для них.

Можно было запустить playbook и так (то есть, писать не полный путь к
файлу retry):

::

    $ ansible-playbook 1_show_commands_with_raw.yml --limit @1_show_commands_with_raw.retry

Параметр --limit очень полезная вещь. Он позволяет ограничивать, для
каких хостов или групп будет выполняться playbook, при этом не меняя сам
playbook.

Например, таким образом playbook можно запустить только для
маршрутизатора 192.168.100.1:

::

    $ ansible-playbook 1_show_commands_with_raw.yml --limit 192.168.100.1

Идемпотентность
~~~~~~~~~~~~~~~

Модули Ansible идемпотентны. Это означает, что модуль можно выполнять
сколько угодно раз, но при этом модуль будет выполнять изменения, только
если система не находится в желаемом состоянии.

Но есть исключения из такого поведения. Например, модуль raw всегда
вносит изменения. Поэтому при выполнении playbook выше всегда
отображалось состояние changed.

Но, если, например, в задаче указано, что на сервер Linux надо
установить пакет httpd, то он будет установлен только в том случае, если
его нет. То есть, действие не будет повторяться снова и снова при каждом
запуске, а лишь тогда, когда пакета нет.

Аналогично и с сетевым оборудованием. Если задача модуля - выполнить
команду в конфигурационном режиме, а она уже есть на устройстве, модуль
не будет вносить изменения.
