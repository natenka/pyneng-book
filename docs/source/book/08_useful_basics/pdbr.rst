.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

pdbr: rich + pdb
----------------

Модуль `pdbr <https://github.com/cansarigol/pdbr>`__ это одна из разновидностей
pdb, которая добавляет несколько полезных команд для начинающих:

* v (vars) - таблица локальных переменных
* vt (varstree) - дерево локальных переменных
* inspect - вывод rich.inspect для объекта

Установка pdbr:

::

    pip install pdbr

Дополнительные команды pdbr:

.. code:: python

    (Pdbr) vars
                                     List of local variables
                 ╷                                                          ╷
      Variable   │ Value                                                    │ Type
    ╶────────────┼──────────────────────────────────────────────────────────┼────────────────╴
      vlans      │ ['1', '2', '3', 'test', '4', '5', 'switchport allowed    │ <class 'list'>
                 │ vlans add']                                              │
      vlans_list │ [1, 2, 3]                                                │ <class 'list'>
      vl         │ test                                                     │ <class 'str'>
      new_vl     │ 3                                                        │ <class 'int'>
                 ╵                                                          ╵

    (Pdbr) varstree
    Variables
    ├── <class 'int'>
    │   └── new_vl: 3
    ├── <class 'list'>
    │   ├── vlans: ['1', '2', '3', 'test', '4', '5', 'switchport allowed vlans add']
    │   └── vlans_list: [1, 2, 3]
    └── <class 'str'>
        └── vl: test

    (Pdbr) inspect vlans
    ╭──────────────────────────────────── <class 'list'> ────────────────────────────────────╮
    │ Built-in mutable sequence.                                                             │
    │                                                                                        │
    │ ╭────────────────────────────────────────────────────────────────────────────────────╮ │
    │ │ ['1', '2', '3', 'test', '4', '5', 'switchport allowed vlans add']                  │ │
    │ ╰────────────────────────────────────────────────────────────────────────────────────╯ │
    │                                                                                        │
