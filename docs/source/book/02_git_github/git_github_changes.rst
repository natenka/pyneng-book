Работа со своим репозиторием
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

В данной главе описывается, как работать с репозиторием, находящемся на
Вашей локальной машине.

Создание репозитория на GitHub
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Для создания репозитория на GitHub нужно:

-  залогиниться на `GitHub <https://github.com/>`__;
-  в правом верхнем углу нажать плюс и выбрать "New repository", чтобы
   создать новый репозиторий;
-  в открывшемся окне надо ввести название репозитория;

Можно поставить галку "Initialize this repository with a README". Это
создаст файл README.md, в котором будет находиться только название
репозитория.

.. figure:: https://raw.githubusercontent.com/natenka/PyNEng/master/images/git/github_new_repo.png
   :alt: create

   create
Клонирование репозитория с GitHub
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Для локальной работы с репозиторием его нужно клонировать.

Для этого используется команда git clone:

::

    $ git clone ssh://git@github.com/pyneng/online-2-natasha-samoylenko.git
    Cloning into 'online-2-natasha-samoylenko'...
    remote: Counting objects: 241, done.
    remote: Compressing objects: 100% (191/191), done.
    remote: Total 241 (delta 43), reused 239 (delta 41), pack-reused 0
    Receiving objects: 100% (241/241), 119.60 KiB | 0 bytes/s, done.
    Resolving deltas: 100% (43/43), done.
    Checking connectivity... done.

По сравнению с приведённой в этом листинге командой, Вам нужно изменить:

-  имя пользователя "pyneng" на имя своего пользователя на GitHub;
-  имя репозитория "online-2-natasha-samoylenko" на имя своего
   репозитория на GitHub.

В итоге, в текущем каталоге, в котором была выполнена команда git clone,
появится каталог с именем репозитория, в моём случае –
"online-2-natasha-samoylenko". В этом каталоге теперь находится
содержимое репозитория на GitHub.

Работа с репозиторием
^^^^^^^^^^^^^^^^^^^^^

Предыдущая команда не просто скопировала репозиторий чтобы использовать
его локально, но и настроила соответствующим образом Git:

-  создан каталог .git;
-  скачаны все данные репозитория;
-  скачаны все изменения, которые были в репозитории;
-  репозиторий на GitHub настроен как remote для локального репозитория.

Теперь готов полноценный локальный репозиторий Git, в котором Вы можете
работать. Обычно последовательность работы будет такой:

-  перед началом работы, синхронизация локального содержимого с GitHub
   командой git pull;
-  изменение файлов репозитория;
-  добавление изменённых файлов в staging командой git add;
-  фиксация изменений через коммит командой git commit;
-  передача локальных изменений в репозитории на GitHub командой git
   push.

При работе с заданиями на работе и дома, надо обратить особое внимание
на первый и последний шаг:

-  первый шаг – обновление локального репозитория;
-  последний шаг – загрузка изменений на GitHub.

Синхронизация локального репозитория с удалённым
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Все команды выполняются внутри каталога репозитория (в примере выше -
online-2-natasha-samoylenko).

Если содержимое локального репозитория одинаково с удалённым, вывод
будет таким:

::

    $ git pull
    Already up-to-date.

Если были изменения, вывод будет примерно таким:

::

    $ git pull
    remote: Counting objects: 5, done.
    remote: Compressing objects: 100% (1/1), done.
    remote: Total 5 (delta 4), reused 5 (delta 4), pack-reused 0
    Unpacking objects: 100% (5/5), done.
    From ssh://github.com/pyneng/online-2-natasha-samoylenko
       89c04b6..fc4c721  master     -> origin/master
    Updating 89c04b6..fc4c721
    Fast-forward
     exercises/03_data_structures/task_3_3.py | 2 ++
     1 file changed, 2 insertions(+)

Добавление новых файлов или изменений в существующих
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Если необходимо добавить конкретный файл (в данном случае – README.md),
нужно дать команду "git add README.md". Добавление всех файлов текущей
директории производится командой "git add .".

Коммит
^^^^^^

При выполнении коммита обязательно надо указать сообщение. Лучше, если
сообщение будет со смыслом, а не просто "update" или подобное. Коммит
делается командой, подобной "git commit -m "Сделал задания 4.1-4.3"".

Push на GitHub
^^^^^^^^^^^^^^

Для загрузки всех локальных изменений на GitHub используется команда git
push:

::

    $ git push origin master
    Counting objects: 5, done.
    Compressing objects: 100% (5/5), done.
    Writing objects: 100% (5/5), 426 bytes | 0 bytes/s, done.
    Total 5 (delta 4), reused 0 (delta 0)
    remote: Resolving deltas: 100% (4/4), completed with 4 local objects.
    To ssh://git@github.com/pyneng/online-2-natasha-samoylenko.git
       fc4c721..edcf417  master -> master

Перед выполнением git push можно выполнить команду "git log -p
origin/master.." – она покажет, какие изменения Вы собираетесь добавлять
в свой репозиторий на GitHub.
