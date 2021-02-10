Переменные класса
~~~~~~~~~~~~~~~~~


Помимо переменных экземпляра, существуют также переменные класса. Они
создаются, при указании переменных внутри самого класса, не метода:

.. code:: python

    class Network:
        all_allocated_ip = []

        def __init__(self, network):
            self.network = network
            self.hosts = tuple(
                str(ip) for ip in ipaddress.ip_network(network).hosts()
            )
            self.allocated = []

        def allocate(self, ip):
            if ip in self.hosts:
                if ip not in self.allocated:
                    self.allocated.append(ip)
                    type(self).all_allocated_ip.append(ip)
                else:
                    raise ValueError(f"IP-адрес {ip} уже находится в allocated")
            else:
                raise ValueError(f"IP-адрес {ip} не входит в сеть {self.network}")

К переменным класса можно обращаться по-разному:

* ``self.all_allocated_ip``
* ``Network.all_allocated_ip``
* ``type(self).all_allocated_ip``

Вариант ``self.all_allocated_ip`` позволяет обратиться к значению переменной
класса или добавить элемент, если переменная класса изменяемый тип данных.
Минус этого варианта в том, что если в методе написать
``self.all_allocated_ip = ...``, вместо изменения переменной класса,
будет создана переменная экземпляра.

Вариант ``Network.all_allocated_ip`` будет работать корректно, но небольшой минус
этого варианта в том, что имя класса прописано вручную.
Вместо него можно использовать третий вариант ``type(self).all_allocated_ip``,
так как ``type(self)`` возвращает класс.



Теперь у класса есть переменная all_allocated_ip в которую записываются
все IP-адреса, которые выделены в сетях:

.. code:: python

    In [3]: net1 = Network("10.1.1.0/29")

    In [4]: net1.allocate("10.1.1.1")
       ...: net1.allocate("10.1.1.2")
       ...: net1.allocate("10.1.1.3")
       ...:

    In [5]: net1.allocated
    Out[5]: ['10.1.1.1', '10.1.1.2', '10.1.1.3']

    In [6]: net2 = Network("10.2.2.0/29")

    In [7]: net2.allocate("10.2.2.1")
       ...: net2.allocate("10.2.2.2")
       ...:

    In [9]: net2.allocated
    Out[9]: ['10.2.2.1', '10.2.2.2']

    In [10]: Network.all_allocated_ip
    Out[10]: ['10.1.1.1', '10.1.1.2', '10.1.1.3', '10.2.2.1', '10.2.2.2']

Переменная доступна не только через класс, но и через экземпляры:

.. code:: python

    In [40]: Network.all_allocated_ip
    Out[40]: ['10.1.1.1', '10.1.1.2', '10.1.1.3', '10.2.2.1', '10.2.2.2']

    In [41]: net1.all_allocated_ip
    Out[41]: ['10.1.1.1', '10.1.1.2', '10.1.1.3', '10.2.2.1', '10.2.2.2']

    In [42]: net2.all_allocated_ip
    Out[42]: ['10.1.1.1', '10.1.1.2', '10.1.1.3', '10.2.2.1', '10.2.2.2']


