Синтаксис шаблонов Jinja2
-------------------------

До сих пор в примерах шаблонов Jinja2 использовалась только подстановка
переменных. Это самый простой и понятный пример использования шаблонов.
Но синтаксис шаблонов Jinja на этом не ограничивается.

В шаблонах Jinja2 можно использовать: 

* переменные 
* условия (if/else)
* циклы (for) 
* фильтры - специальные встроенные методы, которые
  позволяют делать преобразования переменных 
* тесты - используются для проверки, 
  соответствует ли переменная какому-то условию

Кроме того, Jinja поддерживает наследование между шаблонами, а также
позволяет добавлять содержимое одного шаблона в другой.

В этом разделе рассматриваются только основы этих возможностей. Подробнее о шаблонах Jinja2
можно почитать в `документации <http://jinja.pocoo.org/docs/dev/templates/>`__.

.. note::

    Все файлы, которые используются как примеры в этом подразделе,
    находятся в каталоге 3_template_syntax/

Для генерации шаблонов будет использоваться скрипт cfg_gen.py

.. code:: python

    # -*- coding: utf-8 -*-
    from jinja2 import Environment, FileSystemLoader
    import yaml
    import sys
    import os

    #$ python cfg_gen.py templates/for.txt data_files/for.yml
    template_dir, template_file = os.path.split(sys.argv[1])

    vars_file = sys.argv[2]

    env = Environment(
        loader=FileSystemLoader(template_dir),
        trim_blocks=True,
        lstrip_blocks=True)
    template = env.get_template(template_file)

    with open(vars_file) as f:
        vars_dict = yaml.safe_load(f)

    print(template.render(vars_dict))

Для того, чтобы посмотреть на результат, нужно вызвать скрипт и передать
ему два аргумента: 

* шаблон 
* файл с переменными в формате YAML

Результат будет выведен на стандартный поток вывода.

Пример запуска скрипта:

::

    $ python cfg_gen.py templates/variables.txt data_files/vars.yml

Параметры trim_blocks и lstrip_blocks описаны в следующем
подразделе.

.. toctree::
   :maxdepth: 1

   whitespace_control
   syntax_variables
   syntax_for
   syntax_if
   syntax_filter
   syntax_test
   assignments
   include
