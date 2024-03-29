.. meta::
   :http-equiv=Content-Type: text/html; charset=utf-8

Количество потоков
------------------

Сколько потоков нужно использовать при подключении к оборудованию?
На этот вопрос нет однозначного ответа. Количество потоков, как минимум,
зависит от того на каком компьютере выполняется 
скрипт (ОС, память, процессор), от самой сети (задержек).

Поэтому, вместо поиска идеального количества потоков, надо замерить количество на своем
компьютере, сети, скрипте. Например, в примерах к этому разделу есть скрипт 
netmiko_count_threads.py, который запускает одну и ту же функцию с разным 
количеством потоков и выводит информацию о времени выполнения.
В функции по умолчанию используется небольшое количество устройств из файла devices_all.yaml
и небольшое количество потоков, однако его можно адаптировать к любому количеству для своей сети.

Пример подключения к 5000 устройств с разным количеством потоков:

::

    Количество устройств: 5460

    #30 потоков
    ----------------------------------------
    Время выполнения: 0:09:17.187867

    #50 потоков
    ----------------------------------------
    Время выполнения: 0:09:17.604252

    #70 потоков
    ----------------------------------------
    Время выполнения: 0:09:17.117332

    #90 потоков
    ----------------------------------------
    Время выполнения: 0:09:16.693774

    #100 потоков
    ----------------------------------------
    Время выполнения: 0:09:17.083294

    #120 потоков
    ----------------------------------------
    Время выполнения: 0:09:17.945270

    #140 потоков
    ----------------------------------------
    Время выполнения: 0:09:18.114993

    #200 потоков
    ----------------------------------------
    Время выполнения: 0:11:12.951247

    #300 потоков
    ----------------------------------------
    Время выполнения: 0:14:03.790432

В этом случае время выполнения с 30 потоками и 120 потоков одинаковое, а после время только
увеличивается. 
Так происходит потому что на переключение между потоками также тратится много времени, 
чем больше потоков, тем больше переключений. 
И с какого-то момента нет смысла увеличивать количество потоков. Тут оптимальным количеством можно считать, например, 50 потоков. Тут мы берем не 30 чтобы сделать запас. 
