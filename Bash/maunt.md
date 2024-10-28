
---

Команда `mount` в Unix-подобных системах отображает информацию о текущих смонтированных файловых системах и управляет монтированием (подключением) и размонтированием (отключением) устройств.

### Основные функции команды `mount`
1. **Просмотр текущих монтированных файловых систем**:
   - Запуск команды без аргументов (`mount`) выводит список всех смонтированных файловых систем с информацией об их устройстве, точке монтирования, типе файловой системы и параметрах монтирования. Пример:
     ```plaintext
     /dev/sda1 on / type ext4 (rw,relatime)
     tmpfs on /run type tmpfs (rw,nosuid,nodev,mode=755)
     ```

2. **Монтирование устройства**:
   - Команда позволяет вручную монтировать устройство в конкретную точку монтирования:
     ```bash
     mount /dev/sdb1 /mnt
     ```
   - Здесь устройство `/dev/sdb1` монтируется в каталог `/mnt`. Если тип файловой системы не указан, `mount` попытается определить его автоматически.

3. **Опции монтирования**:
   - `mount` поддерживает множество параметров, влияющих на поведение монтируемого устройства, например:
     - `-o ro` — монтировать только для чтения.
     - `-o rw` — монтировать для чтения и записи.
     - `-o noexec` — запретить выполнение файлов на этом разделе.
     - `-o nosuid` — игнорировать файлы с set-user-ID и set-group-ID, повышая безопасность.
   - Пример использования с параметрами:
     ```bash
     mount -o ro /dev/sdb1 /mnt
     ```

4. **Монтирование с указанием типа файловой системы**:
   - Для нестандартных файловых систем можно указать их тип с помощью параметра `-t`, например:
     ```bash
     mount -t vfat /dev/sdb1 /mnt
     ```

5. **Размонтирование файловой системы**:
   - Используется команда `umount`, чтобы размонтировать устройство и завершить работу с ним:
     ```bash
     umount /mnt
     ```

### Пример вывода команды `mount`
Запуск `mount` без аргументов может вернуть следующий результат:
```plaintext
/dev/sda1 on / type ext4 (rw,relatime)
tmpfs on /run type tmpfs (rw,nosuid,nodev,mode=755)
```
Каждая строка описывает:
- Устройство (`/dev/sda1`),
- Точку монтирования (`/`),
- Тип файловой системы (`ext4`),
- Параметры монтирования (`rw,relatime`).

`mount` — это основная утилита для работы с файловыми системами, которая позволяет гибко управлять их монтированием и диагностировать текущее состояние системы.