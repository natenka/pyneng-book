Пример класса
~~~~~~~~~~~~~

Пример класса, который описывает сеть:

.. code:: python

    class Network:
        def __init__(self, network):
            self.network = network
            self.hosts = tuple(str(ip) for ip in ipaddress.ip_network(network).hosts())
            self.allocated = []

        def allocate(self, ip):
            if ip in self.hosts:
                if ip not in self.allocated:
                    self.allocated.append(ip)
                else:
                    raise ValueError(f"IP-адрес {ip} уже находится в allocated")
            else:
                raise ValueError(f"IP-адрес {ip} не входит в сеть {self.network}")

Использование класса:

.. code:: python

    In [2]: net1 = Network("10.1.1.0/29")

    In [3]: net1.allocate("10.1.1.1")

    In [4]: net1.allocate("10.1.1.2")

    In [5]: net1.allocated
    Out[5]: ['10.1.1.1', '10.1.1.2']

    In [6]: net1.allocate("10.1.1.100")
    ---------------------------------------------------------------------------
    ValueError                                Traceback (most recent call last)
    <ipython-input-6-9a4157e02c78> in <module>
    ----> 1 net1.allocate("10.1.1.100")

    <ipython-input-1-c5255d37a7fd> in allocate(self, ip)
         12                 raise ValueError(f"IP-адрес {ip} уже находится в allocated")
         13         else:
    ---> 14             raise ValueError(f"IP-адрес {ip} не входит в сеть {self.network}")
         15

    ValueError: IP-адрес 10.1.1.100 не входит в сеть 10.1.1.0/29



