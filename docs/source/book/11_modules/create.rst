Создание своих модулей
----------------------

Так как модуль - это просто файл с расширение .py и кодом Python, мы
можем легко создать несколько своих модулей.

Например, разделим скрипт из раздела `Совмещение for и
if <../06_control_structures/2b_for_if.md>`__ на несколько частей:
шаблоны портов, данные и формирование команд будут в разных файлах.

Файл sw\_int\_templates.py:

.. code:: python

    access_template = ['switchport mode access',
                       'switchport access vlan',
                       'spanning-tree portfast',
                       'spanning-tree bpduguard enable']

    trunk_template = ['switchport trunk encapsulation dot1q',
                      'switchport mode trunk',
                      'switchport trunk allowed vlan']

    l3int_template = ['no switchport', 'ip address']

Файл sw\_data.py:

.. code:: python

    sw1_fast_int = {
                    'access':{
                             '0/12':'10',
                             '0/14':'11',
                             '0/16':'17'}}

Совмещаем всё вместе в файле generate\_sw\_int\_cfg.py:

.. code:: python

    import sw_int_templates as sw_temp
    from sw_data import sw1_fast_int

    def generate_access_cfg(sw_dict):
        result = []
        for intf, vlan in sw_dict['access'].items():
            result.append('interface FastEthernet' + intf)
            for command in sw_temp.access_template:
                if command.endswith('access vlan'):
                    result.append(' {} {}'.format(command, vlan))
                else:
                    result.append(' {}'.format(command))
        return result


    print('\n'.join(generate_access_cfg(sw1_fast_int)))

В первых двух строках импортируются объекты из других файлов: \*
``import sw_int_templates`` - импорт всего из файла \* пример
использования одного из шаблонов: ``sw_int_templates.access_template``
\* ``from sw_data import sw1_fast_int`` - из модуля sw\_data
импортируется только sw1\_fast\_int \* при таком импорте можно напрямую
обращаться к имени sw1\_fast\_int

Результат выполнения скрипта:

::

    $ python generate_sw_int_cfg.py
    interface FastEthernet0/12
     switchport mode access
     switchport access vlan 10
     spanning-tree portfast
     spanning-tree bpduguard enable
    interface FastEthernet0/14
     switchport mode access
     switchport access vlan 11
     spanning-tree portfast
     spanning-tree bpduguard enable
    interface FastEthernet0/16
     switchport mode access
     switchport access vlan 17
     spanning-tree portfast
     spanning-tree bpduguard enable

