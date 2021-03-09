.. raw:: latex

   \newpage

.. _working_env:


Подготовка рабочего окружения
-----------------------------

Для выполнения заданий книги можно использовать несколько вариантов:

-  взять подготовленную виртуалку vmware или vagrant (virtualbox)
-  подготовить виртуалку самостоятельно
-  использовать вм или какой-то сервис в облаке
-  работать без создания виртуальной машины

Подготовленные VM
~~~~~~~~~~~~~~~~~

Подготовлены виртуальные машины, в которых установлены:

-  Debian 9.9
-  Python 3.7 и 3.8 в виртуальном окружении
-  IPython и другие модули, которые потребуются для выполнения заданий
-  текстовые редакторы vim, Geany, Mu
-  GNS3 для работы с сетевым оборудованием


Есть два варианта подготовленных виртуальных машин (по ссылкам инструкции для каждого варианта, в которых есть ссылки на образ и инструкция как работать с GNS3):

-  `vagrant <https://docs.google.com/document/d/1tIb8prINPM7uhyFxIhSSIF1-jckN_OWkKaO8zHQus9g/edit?usp=sharing>`__
-  `vmware <https://drive.google.com/open?id=1r7Si9xTphdWp79sKxDhVk2zjWGggfy5Z6h8cKCLP5Cs>`__

Подготовка виртуальной машины/хоста самостоятельно
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  `Инструкция для подготовки Linux <https://pyneng.github.io/docs/pynenglinux/>`__
-  `Нюансы подготовки и выполнения заданий на Windows <https://natenka.github.io/pyneng/pyneng-on-windows/>`__

Список модулей, которые нужно установить:

::

    pip install pytest pytest-clarity pyyaml tabulate jinja2 textfsm pexpect netmiko graphviz

Также надо установить graphviz принятым способом в ОС (пример для debian):

::

    apt-get install graphviz

Облачные сервисы
~~~~~~~~~~~~~~~~

Ещё один вариант – использовать один из следующих сервисов:

-  `repl.it <https://repl.it/>`__ – этот сервис предоставляет
   онлайн-интерпретатор Python, а также графический редактор. `Пример
   использования <https://repl.it/KSIp/3/>`__.
-  `PythonAnywhere <https://www.pythonanywhere.com/>`__ - выделяет
   отдельную виртуалку, но в бесплатном варианте вы можете работать
   только из командной строки, то есть, нет графического текстового
   редактора;

Сетевое оборудование
~~~~~~~~~~~~~~~~~~~~

К 18 разделу книги, нужно подготовить виртуальное или реальное сетевое оборудование.

Все примеры и задания, в которых встречается сетевое оборудование, используют
одинаковое количество устройств: три маршрутизатора с такими базовыми настройками:

* пользователь: cisco
* пароль: cisco
* пароль на режим enable: cisco
* SSH версии 2 (обязательно именно версия 2), Telnet
* IP-адреса маршрутизаторов: 192.168.100.1, 192.168.100.2, 192.168.100.3
* IP-адреса должны быть доступны из виртуалки на которой вы выполняете задания
  и могут быть назначены на физических/логических/loopback интерфейсах


Топология может быть произвольной. Пример топологии:

.. figure:: https://raw.githubusercontent.com/pyneng/pyneng.github.io/master/assets/images/gns3_network.png


Базовый конфиг:

::

    hostname R1
    !
    no ip domain lookup
    ip domain name pyneng
    !
    crypto key generate rsa modulus 1024
    ip ssh version 2
    !
    username cisco password cisco
    enable secret cisco
    !
    line vty 0 4
     logging synchronous
     login local
     transport input telnet ssh


На каком-то интерфейсе надо настроить IP-адрес

::

    interface ...
     ip address 192.168.100.1 255.255.255.0


Алиасы (по желанию)

::

    !
    alias configure sh do sh
    alias exec ospf sh run | s ^router ospf
    alias exec bri show ip int bri | exc unass
    alias exec id show int desc
    alias exec top sh proc cpu sorted | excl 0.00%__0.00%__0.00%
    alias exec c conf t
    alias exec diff sh archive config differences nvram:startup-config system:running-config
    alias exec desc sh int desc | ex down
    alias exec bgp sh run | s ^router bgp


При желании можно настроить `EEM applet <http://xgu.ru/wiki/Embedded_Event_Manager>`__
для отображения команд, которые вводит пользователь:

::

    !
    event manager applet COMM_ACC
     event cli pattern ".*" sync no skip no occurs 1
     action 1 syslog msg "User $_cli_username entered $_cli_msg on device $_cli_host "
    !

