# Создание загрузочной системы Legacy Boot MBR
!> Если ваша система поддерживает UEFI, вам следует пропустить эту страницу и настроить GRUB с поддержкой UEFI.

!> Неправильная настройка GRUB может сделать вашу систему неработоспособной без альтернативного загрузочного устройства, такого как CD-ROM или загрузочный USB-накопитель. Этот раздел не требуется для загрузки вашей созданной системы. Вы можете просто изменить свой текущий загрузчик, например Grub-Legacy, GRUB2 или LILO.

## Соглашения о наименованиях GRUB

GRUB использует собственную структуру именования дисков и разделов в виде (``hdn, m``), где ``n``- номер жесткого диска, а ``m`` - номер раздела. Номер жесткого диска начинается с нуля, но номер раздела начинается с единицы для обычных разделов и пяти для расширенных разделов. 

Например, раздел `sda1` - это (`hd0,1`) для GRUB, а `sdb3` - (`hd1,3`). В отличие от Linux, GRUB не считает приводы CD-ROM жесткими дисками. Например, если вы используете компакт-диск на `hdb` и второй диск на `hdc`, этот второй диск все равно будет (`hd1`).

## Настройка

GRUB записывает данные на первую физическую дорожку диска. Эта область не является частью какой-либо файловой системы. Программы  получают доступ к модулям GRUB в загрузочном разделе. Расположение по умолчанию - `/boot/grub/`.

Расположение загрузочного раздела - это выбор пользователя, который влияет на конфигурацию. Одна из рекомендаций - создать отдельный небольшой (рекомендуемый размер 200 МБ) раздел только для загрузочной информации. Таким образом, каждая система, может получить доступ к одним и тем же файлам загрузки, и доступ может быть сделан из любой загруженной системы. Если вы решите это сделать, вам нужно будет смонтировать отдельный раздел, переместить все файлы в текущем каталоге `/boot` (например, ядро ​​Linux, которое вы только что создали в предыдущем разделе) в новый раздел. Затем вам нужно будет размонтировать раздел и перемонтировать его как `/boot`. Если вы это сделаете, обязательно обновите `/etc/fstab`.

Использование c текущим разделом будет работать, но настройка для нескольких систем будет сложнее.

Используя приведенную выше информацию, определите подходящее обозначение для корневого раздела (или загрузочного раздела, если используется отдельный). В следующем примере предполагается, что корневой (или отдельный загрузочный) раздел - это ``sda2``.

Установите файлы GRUB в `/boot/grub/` и настройте загрузочную дорожку:

!> Следующая команда перезапишет текущий загрузчик. Не запускайте команду, если это нежелательно, например, при использовании стороннего диспетчера загрузки для управления основной загрузочной записью (MBR).

?> Если система была загружена с использованием UEFI, ``grub-install`` попытается установить файлы для цели ``x86_64-efi``, но эти файлы не были установлены. Если это так, добавьте ``--target i386-pc`` к команде.

```bash
grub-install /dev/sda
```

## Создание конфигурационного файла

!> Существует команда `grub-mkconfig`, которая может автоматически записывать файл конфигурации. Она использует набор скриптов в `/etc/grub.d/` и уничтожит любые сделанные вами настройки. Эти сценарии предназначены в первую очередь для дистрибутивов без исходного кода и не рекомендуются к использованию. Если вы установите коммерческий дистрибутив Linux, есть большая вероятность, что эта программа будет запущена. Обязательно сделайте резервную копию файла ``grub.cfg``.

Создайте файл ``/boot/grub/grub.cfg``:

<pre class="pre">
cat > /boot/grub/grub.cfg << "EOF"
# Begin /boot/grub/grub.cfg
set default=0
set timeout=5

insmod ext2
set root=(hd0,2)

menuentry "GNU/Linux, Linux" {
        linux /boot/vmlinuz root=/dev/sda2 ro
}
EOF

</pre>

?> Для GRUB файлы ядра относятся к используемому разделу. Если вы использовали отдельный раздел / boot, удалите / boot из указанной выше строки linux. Вам также нужно будет изменить строку установленного корня, чтобы она указывала на загрузочный раздел.

GRUB - чрезвычайно мощная программа, которая предоставляет огромное количество вариантов загрузки с самых разных устройств, операционных систем и типов разделов. Существует также множество опций для настройки, таких как графические заставки, воспроизведение звуков, ввод мыши и т.д.