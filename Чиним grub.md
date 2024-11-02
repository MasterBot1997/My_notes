
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

