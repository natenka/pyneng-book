.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

.. raw:: latex

   \newpage

.. _files_index:

7. Работа с файлами
============================

В реальной жизни для того чтобы полноценно использовать всё, что
рассматривалось до этого раздела, надо разобраться как работать с
файлами.

При работе с сетевым оборудованием (и не только), файлами могут быть:

* конфигурации (простые, не структурированные текстовые файлы)

  * работа с ними рассматривается в этом разделе

* шаблоны конфигураций
  
  * как правило, это какой-то специальный формат файлов.
  * в разделе `Шаблоны конфигураций с Jinja <../20_jinja2/>`__ рассматривается
    использование Jinja2 для создания шаблонов конфигураций
    
* файлы с параметрами подключений

  * как правило, это структурированные файлы, в каком-то определенном формате: YAML, JSON, CSV

    * в разделе `Сериализация данных <../17_serialization/>`__ рассматривается, как работать с такими  файлами

* другие скрипты Python
  
  * в разделе `Модули <../11_modules/>`__ рассматривается, как работать с модулями (другими скриптами Python)

В этом разделе рассматривается работа с простыми текстовыми файлами.
Например, конфигурационный файл Cisco.

В работе с файлами есть несколько аспектов:

* открытие/закрытие
* чтение
* запись

В этом разделе рассматривается только необходимый минимум для работы с файлами. Подробнее в
`документации Python <https://docs.python.org/3/library/stdtypes.html#bltin-file-objects>`__.

.. toctree::
   :maxdepth: 1
   :hidden:

   open
   read
   write
   close
   with
   working_with_files
   further_reading
   ../../exercises/07_exercises
