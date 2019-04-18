Синтаксис шаблонов Jinja2
-------------------------

До сих пор в примерах шаблонов Jinja2 использовалась только подстановка
переменных. Это самый простой и понятный пример использования шаблонов.
Но синтаксис шаблонов Jinja на этом не ограничивается.

В шаблонах Jinja2 можно использовать: \* переменные \* условия (if/else)
\* циклы (for) \* фильтры - специальные встроенные методы, которые
позволяют делать преобразования переменных \* тесты - используются для
проверки, соответствует ли переменная какому-то условию

Кроме того, Jinja поддерживает наследование между шаблонами, а также
позволяет добавлять содержимое одного шаблона в другой.

Мы разберемся с основами этих возможностей. Подробнее о шаблонах Jinja2
можно почитать в
`документации <http://jinja.pocoo.org/docs/dev/templates/>`__.

    Все файлы, которые используются как примеры в этом подразделе,
    находятся в каталоге 3\_template\_syntax/

Для генерации шаблонов будет использоваться скрипт cfg\_gen.py

.. code:: python

    # -*- coding: utf-8 -*-
    from jinja2 import Environment, FileSystemLoader
    import yaml
    import sys
    import os

    TEMPLATE_DIR, template = os.path.split(sys.argv[1])
    VARS_FILE = sys.argv[2]

    env = Environment(loader=FileSystemLoader(TEMPLATE_DIR),
                      trim_blocks=True, lstrip_blocks=True)
    template = env.get_template(template)

    vars_dict = yaml.load(open(VARS_FILE))

    print(template.render(vars_dict))

Для того, чтобы посмотреть на результат, нужно вызвать скрипт и передать
ему два аргумента: \* шаблон \* файл с переменными в формате YAML

Результат будет выведен на стандартный поток вывода.

Пример запуска скрипта:

::

    $ python cfg_gen.py templates/variables.txt data_files/vars.yml

    Параметры trim\_blocks и lstrip\_blocks описаны в следующем
    подразделе.
