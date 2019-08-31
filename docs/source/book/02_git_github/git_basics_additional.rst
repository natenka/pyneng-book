Дополнительные возможности
^^^^^^^^^^^^^^^^^^^^^^^^^^

git diff
''''''''

Команда git diff позволяет посмотреть разницу между различными
состояниями. Например, на данный момент, в репозитории внесены изменения
в файл README и .gitignore.

Команда git status показывает, что оба файла изменены

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/git/git_status_5.png

Команда git diff показывает, какие изменения были внесены с момента
последнего коммита

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/git/git_diff.png

Если добавить изменения, внесённые в файлы, в staging командой ``git add`` и
ещё раз выполнить команду ``git diff``, то она ничего не покажет

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/git/git_add_git_diff.png

Чтобы показать отличия между staging и последним коммитом, надо добавить
параметр ``--staged``

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/git/git_diff_staged.png

Закоммитим изменения

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/git/git_commit_2.png

git log
'''''''

Команда git log показывает, когда были выполнены последние изменения

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/git/git_log.png

По умолчанию команда показывает все коммиты, начиная с ближайшего по
времени. С помощью дополнительных параметров можно не только посмотреть
информацию о коммитах, но и то, какие именно изменения были внесены.

Флаг ``-p`` позволяет отобразить отличия, которые были внесены каждым
коммитом

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/git/git_log_p.png

Более короткий вариант вывода можно вывести с флагом ``--stat``

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/git/git_log_stat.png


