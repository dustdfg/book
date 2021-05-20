# Выбор системы инициализации

В операционной системе Linux после завершения загрузки ядра начинается инициализация Linux системы, сервисов и других компонентов. 

За это отвечает процесс инициализации, он запускается ядром сразу после завершения загрузки, имеет ``PID 1``, и будет выполняться пока будет работать система. Обычно (согласно Filesystem Hierarchy Standard) располагается по пути ``/sbin/init``. Существуют отличия в организации работы подсистемы в операционных системах, ведущих родословную от System V и систем в стиле BSD.


В процессе загрузки после инициализации ядра как правило запускается /sbin/init как первый процесс пользовательского режима, и init отвечает за дальнейшую загрузку системы. Для этого запускаются стартовые сценарии, которые выполняют проверку и монтирование файловых систем, запуск необходимых сервисов, настройку ядра (в том числе загрузку модулей ядра согласно установленному оборудованию, настройку IP-адресов, таблиц маршрутизации и другие задачи), запуск графической оболочки. Основная информация для загрузки как правило размещается в ``/etc/inittab``.

?> Данный раздел предоставляет выбор между несколькими системами инициализации. Вам необходимо самостоятельно выбрать желаемую.