.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

Рекомендации по поводу расположения функций в коде
--------------------------------------------------

В `PEP8 <https://pep8.org/>`__ нет рекомендаций по этому поводу.

Если скрипт в одном файле, обычно порядок такой:

1. shebang, file encoding
2. docstring модуля
3. импорт (модули стандартной библиотеки, сторонние модули, свои скрипты)
4. константы
5. все функции в условно произвольном порядке, тут уже надо самостоятельно решить как удобнее 
6. функции/код для создания CLI если есть
7. Часто, если есть код который надо писать глобально создают функцию main
8. ``if __name__ == "__main__":`` и вызов функции main или глобального кода, который вызывает функции


При этом среди функций обычно выбирают для себя какой-то порядок, чтобы он был
плюс-минус однотипным в разных файлах. Например, сначала пишутся общие функцие,
которые не зависят от других функций в файле, потом те что зависят. При этом
обычно есть какой-то порядок выполнения действий: подключились на оборудование
и считали вывод, парсим его, записали результат в файл - тогда соблюдаем этот
порядок в функциях.

.. note::

    `О структуре больших проектов <https://docs.python-guide.org/writing/structure/>`__.
    И еще одна ссылка по этой же теме, с `примерами структуры проектов Flask/Django <https://realpython.com/python-application-layouts/>`__.
