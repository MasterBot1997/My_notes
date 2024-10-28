
---

Файл `fstab` (File Systems Table) в Unix-подобных системах, расположенный по пути `/etc/fstab`, содержит информацию о файловых системах, которые автоматически монтируются при загрузке системы. Этот файл помогает операционной системе понимать, какие файловые системы и устройства подключать, в какую точку монтирования, и с какими параметрами.

### Основные поля файла `fstab`
Каждая строка в `fstab` описывает отдельное устройство или раздел, который система должна монтировать, и содержит следующие столбцы:

1. **Файловая система (File system)**:
   - Указывает путь к устройству, например, `/dev/sda1` для раздела на жестком диске, или UUID устройства, такой как `UUID=1234-5678`.
   - Это может быть и псевдо-файловая система, например, `proc`, которая монтируется для работы с процессами.

2. **Точка монтирования (Mount point)**:
   - Каталог, в который будет монтироваться устройство, например, `/`, `/home`, `/mnt/storage`.

3. **Тип файловой системы (Type)**:
   - Указывает тип файловой системы, например, `ext4`, `xfs`, `vfat`, или `swap` для разделов подкачки. Для специальных псевдо-файловых систем указываются такие типы, как `proc` или `sysfs`.

4. **Опции монтирования (Options)**:
   - Список параметров, влияющих на поведение монтирования, например:
     - `defaults` — стандартные параметры (чтение и запись, автоматическое монтирование).
     - `ro` (read-only) — монтировать только для чтения.
     - `noatime` — не обновлять временные метки доступа, чтобы повысить производительность.
     - `auto` или `noauto` — монтировать автоматически или вручную.
     - `user` или `nouser` — разрешить или запретить обычным пользователям монтировать устройство.
   
5. **Дамп (Dump)**:
   - Этот столбец используется утилитой `dump` для создания резервных копий файловой системы. Значение `0` означает, что копирование для этого раздела отключено, а `1` включает его.

6. **Порядок проверки файловой системы (Pass)**:
   - Задает порядок проверки файловых систем утилитой `fsck` при загрузке системы. Значение `1` присваивается корневой файловой системе, и она проверяется первой, `2` — для других файловых систем, которые проверяются позже, и `0` — для отключения проверки.

### Пример записи в `fstab`
```plaintext
UUID=1234-5678   /home   ext4    defaults,noatime   0   2
```
- Эта запись указывает системе монтировать файловую систему с UUID `1234-5678` в `/home` как `ext4`, с использованием стандартных параметров и отключенными временными метками доступа (`noatime`). Проверка файловой системы включена (`2`).

### Как `fstab` используется системой
При загрузке системы демон `systemd` или процесс инициализации читает файл `fstab` и монтирует все файловые системы и разделы, указанные в нем, согласно их точкам монтирования и опциям.