
`mount` — утилита для подключения файловых систем к директориям.  
`umount` — утилита для их безопасного отключения.  
Используются для управления точками монтирования, USB-накопителями, сетевыми ресурсами и образами дисков.

## 📝 Топ‑20 полезных флагов `mount`
- `-a` — смонтировать все ФС из `/etc/fstab`.
- `-t <FSTYPE>` — указать тип ФС (например, `ext4`, `vfat`, `nfs`).
- `-o <OPTIONS>` — задать параметры монтирования.
- `-o rw` — смонтировать ФС в режиме **чтение/запись**.
- `-o ro` — смонтировать ФС в режиме **только чтение**.
- `-o noexec` — запретить выполнение бинарников.
- `-o remount` — перемонтировать уже смонтированную ФС.
- `-o loop` — монтировать ISO/образы.
- `-o uid=<ID>` — задать владельца ФС по UID.
- `-o gid=<ID>` — задать группу владельцев.
- `-o noatime` — отключить обновление времени доступа к файлам.
- `-o relatime` — обновлять время доступа только если изменено.
- `-o sync` — синхронизированная запись данных.
- `-o async` — асинхронная запись (по умолчанию).
- `-o defaults` — стандартные опции (rw, suid, dev, exec, auto, nouser, async).
- `-v` — подробный вывод.
- `--bind` — привязка директории в другое место.
- `--make-private` — сделать монтирование приватным.
- `--make-shared` — расшарить монтирование для других namespace.
- `--rbind` — рекурсивно смонтировать директорию.

---

# 🚀 Айсберг команд `mount` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`mount`
- Показать список всех смонтированных ФС

`sudo mount /dev/sdb1 /mnt`
- Смонтировать флешку

`sudo umount /mnt`
- Размонтировать флешку

`sudo umount /dev/sdb1`
- Альтернативный способ размонтирования

`lsblk`
- Проверить, где устройство смонтировано


## 🟡 Уровень 2 — Монтирование образов и ISO

`sudo mount -o loop disk.iso /mnt`
- Смонтировать ISO-образ

`sudo umount /mnt`
- Размонтировать ISO

`sudo mount -o loop,ro image.iso /mnt`
- Смонтировать ISO в режиме только чтение

`sudo mount /path/to/disk.img /mnt -o loop`
- Монтирование `.img`-образа

`sudo losetup -fP disk.img`
- Создать loop-устройство для образа


## 🟠 Уровень 3 — Настройка прав и владельцев

`sudo mount -t vfat /dev/sdb1 /mnt -o uid=1000,gid=1000`
- Смонтировать FAT32 с правами конкретного пользователя

`sudo mount -t ntfs-3g /dev/sdb1 /mnt -o rw`
- Монтирование NTFS-диска с правом записи

`sudo mount -t ext4 /dev/sdb1 /mnt -o noexec`
- Запретить выполнение файлов

`sudo mount -t ext4 /dev/sdb1 /mnt -o noatime`
- Не обновлять время доступа

`sudo mount -t ext4 /dev/sdb1 /mnt -o sync`
- Включить синхронную запись


## 🔵 Уровень 4 — NFS, SMB и сетевые диски

`sudo mount -t nfs 192.168.1.100:/data /mnt`
- Подключить NFS-шару

`sudo mount -t nfs -o rw,vers=4 192.168.1.100:/backup /mnt`
- Смонтировать NFS v4 с правом записи

`sudo mount -t cifs //192.168.1.50/share /mnt -o username=user,password=pass`
- Подключить SMB-шару (Windows)

`sudo mount -t cifs //server/share /mnt -o credentials=/root/.smb`
- Использовать файл с логином/паролем

`sudo umount /mnt`
- Отключить сетевой диск


## 🟣 Уровень 5 — Работа с `/etc/fstab`

`sudo nano /etc/fstab`
- Редактировать таблицу монтирования

Пример строки для автоподключения:
```
UUID=XXXX-YYYY /mnt/data ext4 defaults 0 2
```

`sudo mount -a`
- Смонтировать все ФС из `/etc/fstab`

`findmnt`
- Проверить точки монтирования

`sudo systemctl daemon-reload`
- Обновить systemd после редактирования `fstab`


## 🔴 Уровень 6 — Перемонтирование и namespaces

`sudo mount -o remount,rw /mnt`
- Перемонтировать ФС в режим чтения-записи

`sudo mount --bind /data /mnt/data`
- Привязка директории

`sudo mount --rbind /var /mnt/var`
- Рекурсивная привязка

`sudo mount --make-private /mnt`
- Сделать точку монтирования приватной

`sudo mount --make-shared /mnt`
- Сделать точку монтирования разделяемой


## 🟤 Уровень 7 — Гуру‑режим

`sudo mount --bind /old /new && sudo mount --make-private /new`
- Изолированная привязка директории

`sudo unshare --mount /bin/bash`
- Создать новый mount namespace

`sudo mount --make-rshared /mnt`
- Включить рекурсивное разделяемое монтирование

`mount | grep nfs`
- Фильтр по сетевым ресурсам

`findmnt -t ext4`
- Найти все ext4 точки монтирования


## 🧩 Бонус — практические сценарии

**Монтирование ISO-образа:**
```
sudo mount -o loop ubuntu.iso /mnt
```

**Автомонтирование USB:**
```
sudo blkid
sudo nano /etc/fstab
UUID=XXXX-YYYY /media/usb vfat defaults 0 0
sudo mount -a
```

**Монтирование Windows-шары:**
```
sudo mount -t cifs //192.168.1.50/share /mnt -o username=user,password=pass
```

**Перемонтирование диска в rw:**
```
sudo mount -o remount,rw /mnt
```

**Мониторинг всех монтирований в реальном времени:**
```
watch -n 1 mount
```
