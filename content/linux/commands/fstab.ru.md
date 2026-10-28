
`/etc/fstab` (**File System Table**) — конфигурационный файл Linux, который определяет, какие файловые системы монтируются автоматически при загрузке.  
Используется вместе с `mount`, `umount` и `systemd` для управления дисками и точками монтирования.

## 📝 Основные столбцы `/etc/fstab`
Каждая строка в `fstab` содержит **6 полей**:
```
<Устройство>   <Точка_монтирования>   <Тип_ФС>   <Опции>   <dump>   <fsck>
```

1. **Устройство** — путь или UUID (`/dev/sda1` или `UUID=xxxx`).
2. **Точка монтирования** — директория, куда будет монтироваться устройство.
3. **Тип ФС** — например, `ext4`, `vfat`, `ntfs`, `nfs`, `btrfs`.
4. **Опции** — параметры монтирования (`defaults`, `rw`, `noexec`, `noatime` и др.).
5. **dump** — 0 или 1, использовать ли `dump` для бэкапа.
6. **fsck** — порядок проверки ФС при загрузке (`0` — не проверять).

---

## 📝 Топ‑20 полезных опций `fstab`
- `defaults` — стандартный набор (`rw,suid,dev,exec,auto,nouser,async`).
- `rw` — смонтировать с правом записи.
- `ro` — только чтение.
- `noexec` — запрет выполнения бинарников.
- `exec` — разрешить выполнение файлов.
- `nosuid` — игнорировать SUID-биты.
- `nodev` — запрещает доступ к спецфайлам устройств.
- `relatime` — обновлять время доступа только при изменении.
- `noatime` — полностью отключить обновление времени доступа.
- `sync` — все операции записи выполняются синхронно.
- `async` — асинхронная запись (по умолчанию).
- `user` — разрешить монтирование обычным пользователям.
- `nouser` — запрещает монтирование пользователям.
- `uid=<id>` — владелец смонтированной ФС.
- `gid=<id>` — группа владельцев.
- `umask=<val>` — маска прав для смонтированной ФС.
- `errors=remount-ro` — при ошибках монтировать в режиме ro.
- `x-systemd.automount` — автоматическое монтирование при обращении.
- `x-systemd.device-timeout=10s` — таймаут при подключении.
- `comment=<label>` — оставить заметку в конфиге.

---

# 🚀 Айсберг по `fstab` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

**Показать текущий `fstab`:**
```
cat /etc/fstab
```

**Проверить подключенные ФС:**
```
mount | column -t
```

**Смонтировать всё из `fstab`:**
```
sudo mount -a
```

**Проверить точки монтирования:**
```
findmnt
```

**Проверить UUID всех дисков:**
```
blkid
```


## 🟡 Уровень 2 — Автомонтирование USB и дисков

Добавляем строку в `/etc/fstab`:
```
UUID=XXXX-YYYY  /media/usb   vfat  defaults,noauto,user  0  0
```

**Автоматическое монтирование:**
```
sudo mount -a
```

**Монтирование вручную:**
```
sudo mount /media/usb
```

**Проверка точек:**
```
df -h | grep usb
```

**Используем `lsblk` для поиска USB:**
```
lsblk -f
```


## 🟠 Уровень 3 — Монтирование сетевых ресурсов

**Добавить SMB-шару в `/etc/fstab`:**
```
 //192.168.1.50/share   /mnt/smb   cifs  username=user,password=pass,uid=1000,gid=1000   0  0
```

**Использовать NFS:**
```
192.168.1.100:/data  /mnt/nfs  nfs  defaults  0  0
```

**Проверка подключения:**
```
df -h | grep smb
```

**Использовать файл с паролями:**
```
 //server/share   /mnt/share   cifs  credentials=/root/.smb  0  0
```

**Перемонтировать сетевой ресурс:**
```
sudo mount -a
```


## 🔵 Уровень 4 — Продвинутое управление

**Автомонтирование только при доступе:**
```
UUID=XXXX-YYYY  /mnt/data  ext4  defaults,x-systemd.automount  0  2
```

**Настроить таймаут подключения:**
```
UUID=XXXX-YYYY  /mnt/data  ext4  defaults,x-systemd.device-timeout=5s  0  2
```

**Игнорировать ошибку при загрузке:**
```
UUID=XXXX-YYYY  /mnt/backup  ext4  defaults,nofail  0  2
```

**Ограничить пользователям возможность монтирования:**
```
UUID=XXXX-YYYY  /mnt/private  ext4  defaults,nouser  0  2
```

**Монтировать в режиме только чтение:**
```
UUID=XXXX-YYYY  /mnt/archive  ext4  ro  0  2
```


## 🟣 Уровень 5 — Интеграция с `systemd`

**Сгенерировать unit для точки монтирования:**
```
systemctl daemon-reload
systemctl restart local-fs.target
```

**Посмотреть активные точки:**
```
systemctl list-units --type=mount
```

**Перемонтировать всё:**
```
sudo mount -a
```

**Использовать `x-systemd.requires`:**
```
UUID=XXXX-YYYY  /mnt/data  ext4  defaults,x-systemd.requires=network-online.target  0  2
```

**Логирование ошибок монтирования:**
```
journalctl -xe | grep mount
```


## 🔴 Уровень 6 — Гуру‑режим

**Монтирование шифрованных разделов:**
```
/dev/mapper/cryptdisk  /mnt/secure  ext4  defaults  0  2
```

**Автомонтирование через ключ:**
```
UUID=XXXX-YYYY  /mnt/secure  ext4  defaults,credentials=/root/keyfile  0  2
```

**Использовать bind для каталогов:**
```
/data/projects  /mnt/projects  none  bind  0  0
```

**Монтирование tmpfs:**
```
tmpfs  /mnt/tmpfs  tmpfs  defaults,size=2G  0  0
```

**Монтирование overlay для контейнеров:**
```
overlay  /mnt/overlay  overlay  lowerdir=/base,upperdir=/upper,workdir=/work  0  0
```


## 🧩 Бонус — практические сценарии

**Добавить флешку с автомонтированием:**
```
UUID=XXXX-YYYY  /media/usb  vfat  defaults  0  0
```

**Настроить NFS в `fstab`:**
```
192.168.1.100:/data  /mnt/nfs  nfs  defaults  0  0
```

**Создать tmpfs для кэша:**
```
tmpfs  /tmp/cache  tmpfs  defaults,size=1G  0  0
```

**Монтировать каталог в каталог:**
```
/data/logs  /mnt/logs  none  bind  0  0
```

**Пересобрать все точки монтирования:**
```
sudo mount -a
```
