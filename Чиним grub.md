
---

Короче, сделал так:
```
1  lsblk
2  mount /dev/vda1 /mnt
3  mount --bind /dev /mnt/dev
4  mount --bind /proc /mnt/proc
5  mount --bind /sys /mnt/sys
6  mount --bind /boot /mnt/boot
7  chroot /mnt
8  grub-install /dev/vda
9  update-grub
```

При данных действиях ошибка:
```
root@kgkvhbeoer:/# grub-install /dev/vda
Installing for i386-pc platform.
grub-install: error: failed to get canonical path of `aufs'.
```

По совету GPT проверил mount:
```
root@kgkvhbeoer:/# mount
/dev/vda1 on / type ext4 (rw,relatime,data=ordered)
devtmpfs on /dev type devtmpfs (rw,nosuid,size=1022492k,nr_inodes=255623,mode=755)
proc on /proc type proc (rw,relatime)
sysfs on /sys type sysfs (rw,relatime)
aufs on /boot type aufs (rw,relatime,si=3ee6a420815f58,trunc_xino)
```

---

При восствновлении гпт подсказал что проблема может быть в подключенном разделе **aufs** который является восстановочным режимом. поэтому повторил процедуру без добавления раздела **boot**:
```
kgkvhbeoer : ~ [0] # mount /dev/vda1 /mnt && mount --rbind /dev /mnt/dev && mount --rbind /proc /mnt/proc && mount --rbind /sys /mnt/sys && chroot /mnt bash
```

Далее выполнил установку grub:
```
root@kgkvhbeoer:/# grub-install /dev/vda
Installing for i386-pc platform.
Installation finished. No error reported.

root@kgkvhbeoer:/#  update-grub
Sourcing file `/etc/default/grub'
Sourcing file `/etc/default/grub.d/50-cloudimg-settings.cfg'
Generating grub configuration file ...
Warning: os-prober will be executed to detect other bootable partitions.
Its output will be used to detect bootable binaries on them and create new boot entries.
Adding boot menu entry for UEFI Firmware Settings ...
done

root@kgkvhbeoer:/# ll /boot/grub/
total 40
drwxr-xr-x 5 root root  4096 Nov  3 07:12 ./
drwxr-xr-x 3 root root  4096 Nov  3 07:12 ../
drwxr-xr-x 2 root root  4096 Nov  3 07:12 fonts/
-rw------- 1 root root  4105 Nov  3 07:12 grub.cfg
-rw-r--r-- 1 root root  1024 Nov  3 07:12 grubenv
drwxr-xr-x 2 root root 12288 Nov  3 07:12 i386-pc/
drwxr-xr-x 2 root root  4096 Nov  3 07:12 locale/
```

---

Не помогло...

Пробуем дальше
```
kgkvhbeoer : ~ [0] # mount /dev/vda15 /mnt/boot/efi
mount: mount point /mnt/boot/efi does not exist
kgkvhbeoer : ~ [32] # mkdir -p /mnt/boot/efi
kgkvhbeoer : ~ [0] # mount /dev/vda15 /mnt/boot/efi
kgkvhbeoer : ~ [0] # chroot /mnt/
root@kgkvhbeoer:/# ll /boot/
total 13
drwxr-xr-x  4 root root 4096 Nov  3 07:25 ./
drwxr-xr-x 21 root root 4096 Nov  2 16:01 ../
drwxr-xr-x  3 root root  512 Jan  1  1970 efi/
drwxr-xr-x  5 root root 4096 Nov  3 07:12 grub/
root@kgkvhbeoer:/# grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
Installing for x86_64-efi platform.
grub-install: warning: EFI variables cannot be set on this system.
grub-install: warning: You will have to complete the GRUB setup manually.
Installation finished. No error reported.
root@kgkvhbeoer:/# update-grub
Sourcing file `/etc/default/grub'
Sourcing file `/etc/default/grub.d/50-cloudimg-settings.cfg'
Generating grub configuration file ...
Warning: os-prober will not be executed to detect other bootable partitions.
Systems on them will not be added to the GRUB boot configuration.
Check GRUB_DISABLE_OS_PROBER documentation entry.
Adding boot menu entry for UEFI Firmware Settings ...
done

```

Короче говоря, у нас тут нет еще и ядра системы)

Установим его вручную в rescure режиме grub, файлы ядра есть в /dev/vda16

![[Снимок экрана 2024-11-03 103735.png]]

Выполним команды:
```
set root=(hd0,gpt1)
linux (hd0,gpt16)/vmlinuz-6.8.0-44-generic root=/dev/vda1 ro 
initrd (hd0,gpt16)/initrd.img-6.8.0-44-generic
boot
```

Получилось запустить систему)))

Пароль root - **f8S&SARCH&73**

Удалось подключиться))))
Делаем в данном режиме установку и апдейт граб


Итого в нашей структуре:

```
sr0      11:0    1  368K  0 rom
vda     253:0    0   30G  0 disk
├─vda1  253:1    0   29G  0 part /
├─vda14 253:14   0    4M  0 part
├─vda15 253:15   0  106M  0 part /boot/efi
└─vda16 259:0    0  913M  0 part /boot
```

vda1 - хранит файлы системы
vda14 - хз что за зверь
vda 15 - хранит инфу о efi режиме
vda16 - хранит файлы ядра системы для запуска

Пока что могу сделать вывод, что если грохнули boot, то можно смонтировать часть каталогов:
```
kgkvhbeoer : ~ [0] # mount /dev/vda1 /mnt && mount --rbind /dev /mnt/dev && mount --rbind /proc /mnt/proc && mount --rbind /sys /mnt/sys && chroot /mnt bash
```

Далее накатываем grub:
```
root@kgkvhbeoer:/# grub-install /dev/vda
Installing for i386-pc platform.
Installation finished. No error reported.

root@kgkvhbeoer:/#  update-grub
Sourcing file `/etc/default/grub'
Sourcing file `/etc/default/grub.d/50-cloudimg-settings.cfg'
Generating grub configuration file ...
Warning: os-prober will be executed to detect other bootable partitions.
Its output will be used to detect bootable binaries on them and create new boot entries.
Adding boot menu entry for UEFI Firmware Settings ...
done
```

Восстанавливаем efi:
```
kgkvhbeoer : ~ [0] # mkdir -p /mnt/boot/efi
kgkvhbeoer : ~ [0] # mount /dev/vda15 /mnt/boot/efi
kgkvhbeoer : ~ [0] # chroot /mnt
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB

```

Далее выходим из нашего rescure режима и производим восстановление в grub-rescure:
```
ls #получаем список смонтированных директорий файловой системы

#нам потребуется найти корневой каталог(hd0,gpt1) и каталог boot(hd0,gpt16)

#Устанавливаем корневой раздел
grub> set root=(hd0,gpt1)
#Указываем путь к файлам ядра которые лежат в (hd0,gpt16)
grub> linux (hd0,gpt16)/vmlinuz-6.8.0-44-generic root=/dev/vda1 ro
grub> initrd (hd0,gpt16)/initrd.img-6.8.0-44-generic
#Запускаем систему
grub> boot

#Далее должна произойти обычная загрузка системы, тут лучше установить grub повторно и произвести uodate
sudo grub-install /dev/vda 
sudo update-grub

#Проверяем что все ок перезагрузиив систему
sudo reboot
```

