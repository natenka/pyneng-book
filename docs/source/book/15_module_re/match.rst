Функция match
-------------

Функция ``match()``: 

* используется для поиска в начале строки подстроки, которая соответствует шаблону 
* возвращает объект Match, если подстрока найдена 
* возвращает ``None``, если подстрока не найдена

Функция match отличается от search тем, что match всегда ищет совпадение
в начале строки. Например, если повторить пример, который использовался
для функции search, но уже с match:

.. code:: python

    In [2]: import re

    In [3]: log = '%SW_MATM-4-MACFLAP_NOTIF: Host 01e2.4c18.0156 in vlan 10 is flapping between port Gi0/16 and port Gi0/24'

    In [4]: match = re.match(r'Host \S+ '
       ...:                  r'in vlan (\d+) '
       ...:                  r'is flapping between port '
       ...:                  r'(\S+) and port (\S+)', log)
       ...:

Результатом будет None:

.. code:: python

    In [6]: print(match)
    None

Так получилось из-за того, что match ищет слово Host в начале строки. Но
это сообщение находится в середине.

В данном случае можно легко исправить выражение, чтобы функция match
находила совпадение:

.. code:: python

    In [4]: match = re.match(r'\S+: Host \S+ '
       ...:                  r'in vlan (\d+) '
       ...:                  r'is flapping between port '
       ...:                  r'(\S+) and port (\S+)', log)
       ...:

Перед словом Host добавлено выражение ``\S+:``. Теперь совпадение будет
найдено:

.. code:: python

    In [11]: print(match)
    <_sre.SRE_Match object; span=(0, 104), match='%SW_MATM-4-MACFLAP_NOTIF: Host 01e2.4c18.0156 in '>

    In [12]: match.groups()
    Out[12]: ('10', 'Gi0/16', 'Gi0/24')

Пример аналогичен тому, который использовался в функции search, с
небольшими изменениями (файл parse_log_match.py):

.. code:: python

    import re

    regex = (r'\S+: Host \S+ '
             r'in vlan (\d+) '
             r'is flapping between port '
             r'(\S+) and port (\S+)')

    ports = set()

    with open('log.txt') as f:
        for line in f:
            match = re.match(regex, line)
            if match:
                vlan = match.group(1)
                ports.add(match.group(2))
                ports.add(match.group(3))

    print('Петля между портами {} в VLAN {}'.format(', '.join(ports), vlan))

Результат:

::

    $ python parse_log_match.py
    Петля между портами Gi0/19, Gi0/24, Gi0/16 в VLAN 10

