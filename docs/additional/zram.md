# Настройка zram

Если оперативной памяти вашего компьютера недостаточно (3 Гб и меньше), то наиболее быстрым способом решения проблемы является использование подкачки, так как компиляция некоторых пакетов требует мощного ПК, в том числе, большого объёма ОЗУ. Для этих целей можно использовать файл или раздел, но можно и zram.

*Действия производятся на хост-системе*

Для начала нужно загрузить модуль. В системе его может не оказаться, поэтому можно его скомпилировать. Такие дистрибутивы как Ubuntu и прочие предоставляют пакет `zram-config`, в Fedora и Calculate Linux zram активирован по умолчанию. 

Выполнить:
```
modprobe zram num_devices=4
```

В `num_devices` задается количество сжатых блочных устройств, которое будет создано.
Для наиболее оптимального использования CPU стоит учесть: сжатие каждого устройства `zram` однопоточное. Потому я создаю их по количеству ядер. У меня четыре ядра, этого хватит.

При настройке модуля задается фиксированный размер НЕ сжатых данных в байтах

```
SIZE=1536
echo $(($SIZE*1024*1024)) > /sys/block/zram0/disksize
echo $(($SIZE*1024*1024)) > /sys/block/zram1/disksize
echo $(($SIZE*1024*1024)) > /sys/block/zram2/disksize
echo $(($SIZE*1024*1024)) > /sys/block/zram3/disksize
```

В итоге будет создано устройство `/dev/zram0` заданного размера

```
Disk /dev/zram0: 1610 MB, 1610612736 bytes, 393216 sectors
    Units = sectors of 1 * 4096 = 4096 bytes
    Sector size (logical/physical): 4096 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
```

Данные записанные на него будут прозрачно сжаты в памяти. Что делать с ним далее — уже ваш выбор, я на этих устройствах создаю разделы подкачки.

```
mkswap /dev/zram0
mkswap /dev/zram1
mkswap /dev/zram2
mkswap /dev/zram3

swapon /dev/zram0 -p 10
swapon /dev/zram1 -p 10
swapon /dev/zram2 -p 10
swapon /dev/zram3 -p 10
```

Для того, чтобы проверить результат, выполните:
```
swapon -s
```

Далее уже ядро рассчитывает само, какие данные туда перемещать в зависимости от того, как часто вы к ним обращаетесь и как много памяти свободно.

В любом случае, для регулирования отправления данных в swap, настройте wm.swappines, который предоставляет очень быструю и простую конфигурацию.

Так как с помощью zram подкачка находится в ОЗУ, т.е. данные в нём просто сжимаются, то такая подкачка (swap) даже быстрее обычного файла или раздела на жёстком диске.

Взято [отсюда] (https://m.habr.com/ru/post/172137/). 