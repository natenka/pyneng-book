AND
~~~

Оператор AND позволяет группировать несколько условий:

.. code:: sql

    sqlite> select * from switch where model = 'Cisco 3750' and ip LIKE '10.0.%';
    mac             hostname    model       location           ip          vlan
    --------------  ----------  ----------  -----------------  ----------  ----------
    0010.A11A.C1CC  sw1         Cisco 3750  London, Green Str  10.0.255.1  255
    0020.A22A.C2CC  sw2         Cisco 3750  London, Green Str  10.0.255.2  255

    sqlite> select * from switch where model LIKE '%3750%' and ip LIKE '10.0.%';
    mac             hostname    model       location           ip          vlan
    --------------  ----------  ----------  -----------------  ----------  ----------
    0010.A11A.C1CC  sw1         Cisco 3750  London, Green Str  10.0.255.1  255
    0020.A22A.C2CC  sw2         Cisco 3750  London, Green Str  10.0.255.2  255

OR
~~

Оператор OR:

.. code:: sql

    sqlite> select * from switch where model = 'Cisco 3750' or model = 'Cisco 3850';
    mac             hostname    model       location           ip          vlan
    --------------  ----------  ----------  -----------------  ----------  ----------
    0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
    0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
    0010.A11A.C1CC  sw1         Cisco 3750  London, Green Str  10.0.255.1  255
    0020.A22A.C2CC  sw2         Cisco 3750  London, Green Str  10.0.255.2  255
    0030.A3AA.C1CC  sw3         Cisco 3850  London, Green Str  10.255.1.3  255

IN
~~

Оператор IN:

.. code:: sql

    sqlite> select * from switch where model in ('Cisco 3750', 'C3750');
    mac             hostname    model       location           ip          vlan
    --------------  ----------  ----------  -----------------  ----------  ----------
    0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255
    0010.A11A.C1CC  sw1         Cisco 3750  London, Green Str  10.0.255.1  255
    0020.A22A.C2CC  sw2         Cisco 3750  London, Green Str  10.0.255.2  255

NOT
~~~

Оператор NOT:

.. code:: sql

    sqlite> select * from switch where model not in ('Cisco 3750', 'C3750');
    mac             hostname    model       location           ip          vlan
    --------------  ----------  ----------  -----------------  ----------  ----------
    0040.A4AA.C2CC  sw4         Cisco 3850  London, Green Str  10.255.1.4  255
    0050.A5AA.C3CC  sw5         Cisco 3850  London, Green Str  10.255.1.5  255
    0070.A7AA.C5CC  sw7         Cisco 3650  London, Green Str  10.255.1.7  255
    0030.A3AA.C1CC  sw3         Cisco 3850  London, Green Str  10.255.1.3  255

    sqlite> select * from switch where model LIKE '%3750%' and ip not LIKE '10.0.%';
    mac             hostname    model       location           ip          vlan
    --------------  ----------  ----------  -----------------  ----------  ----------
    0060.A6AA.C4CC  sw6         C3750       London, Green Str  10.255.1.6  255

