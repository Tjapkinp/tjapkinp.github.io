
# 💾 `dd`: кратко и по делу
`dd` — мощный инструмент низкоуровневого копирования и преобразования данных.  
Используется для создания образов дисков, клонирования разделов, восстановления данных, тестирования скорости записи/чтения и работы с загрузочными USB.

## 📝 Топ‑20 полезных флагов `dd`
- `if=<file>` — входной файл (input file).
- `of=<file>` — выходной файл (output file).
- `bs=<size>` — размер блока (например, `bs=4M`).
- `count=<n>` — количество блоков для копирования.
- `skip=<n>` — пропустить N блоков во входном файле.
- `seek=<n>` — пропустить N блоков в выходном файле.
- `status=progress` — отображать прогресс выполнения.
- `conv=noerror` — продолжать при ошибках чтения.
- `conv=sync` — выравнивать блоки нулями.
- `conv=fdatasync` — синхронизировать буферы перед завершением.
- `iflag=fullblock` — считывать блоки полностью.
- `oflag=direct` — запись в обход кэша ОС.
- `oflag=sync` — синхронизированная запись каждого блока.
- `ibs=<size>` — размер блока чтения.
- `obs=<size>` — размер блока записи.
- `conv=ucase` — преобразовать текст в верхний регистр.
- `conv=lcase` — преобразовать текст в нижний регистр.
- `conv=swab` — менять байты местами (endianness).
- `count_bytes` — копировать указанное число байт вместо блоков.
- `status=none` — тихий режим без вывода.

---

# 🚀 Айсберг команд `dd` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`dd if=input.img of=output.img bs=4M status=progress`
- Клонирование образа файла

`dd if=/dev/sda of=/dev/sdb bs=64K status=progress`
- Клонирование диска на диск

`dd if=/dev/zero of=file.bin bs=1M count=100`
- Создать файл размером **100 МБ**, заполненный нулями

`dd if=/dev/urandom of=file.bin bs=1M count=10`
- Создать файл **10 МБ** случайных данных

`dd if=/dev/cdrom of=cd.iso status=progress`
- Скопировать CD/DVD в ISO-образ


## 🟡 Уровень 2 — Создание загрузочных USB

`dd if=debian.iso of=/dev/sdX bs=4M status=progress`
- Создать загрузочную флешку

`sync`
- Обязательно синхронизировать буферы после записи

`dd if=archlinux.iso of=/dev/sdb bs=8M oflag=direct status=progress`
- Быстрая запись ISO напрямую

`dd if=ventoy.iso of=/dev/sdc bs=1M conv=fdatasync status=progress`
- Запись ISO с явной синхронизацией

`udisksctl power-off -b /dev/sdX`
- Безопасно извлечь флешку после `dd`


## 🟠 Уровень 3 — Восстановление данных и образов

`dd if=/dev/sda of=backup.img bs=64K conv=noerror,sync`
- Создать резервный образ диска с обработкой ошибок

`dd if=backup.img of=/dev/sda bs=64K status=progress`
- Восстановить образ обратно на диск

`dd if=/dev/sdb of=/dev/null bs=4M status=progress`
- Проверить диск на читаемость

`dd if=/dev/sda of=recovery.img bs=512 skip=2048 count=1000`
- Восстановить 1000 блоков начиная с 2048-го

`dd if=/dev/zero of=/dev/sda bs=1M status=progress`
- Полностью стереть диск нулями ⚠️


## 🔵 Уровень 4 — Тестирование скорости дисков

`dd if=/dev/zero of=testfile bs=1G count=1 oflag=dsync`
- Проверить скорость записи

`dd if=testfile of=/dev/null bs=1G`
- Проверить скорость чтения

`dd if=/dev/zero of=bigfile bs=512K count=10000 status=progress`
- Тест последовательной записи

`dd if=bigfile of=/dev/null bs=512K status=progress`
- Тест последовательного чтения

`hdparm -t /dev/sda`
- Альтернатива `dd` для теста скорости


## 🟣 Уровень 5 — Тонкие настройки

`dd if=/dev/sda of=backup.img bs=64K iflag=fullblock status=progress`
- Гарантированно считывать блоки полностью

`dd if=backup.img of=/dev/sda obs=512 ibs=512`
- Раздельные размеры блоков для чтения и записи

`dd if=/dev/zero of=file.bin bs=1M count=100 conv=fdatasync`
- Явная синхронизация данных при записи

`dd if=input.img of=output.img bs=4M conv=sparse`
- Создать "тонкий" образ, пропуская нули

`dd if=file.txt of=upper.txt conv=ucase`
- Перевести содержимое в верхний регистр


## 🔴 Уровень 6 — Гуру‑режим

`pv < input.img | dd of=/dev/sdX bs=4M`
- Использовать `pv` для красивого прогресса

`dd if=/dev/sda | gzip > backup.img.gz`
- Создать сжатый резервный образ

`gzip -dc backup.img.gz | dd of=/dev/sda`
- Восстановить сжатый образ

`dd if=/dev/sda conv=swab,noerror,sync | dd of=copy.img`
- Инвертировать байты при копировании

`dd if=/dev/mapper/cryptdisk of=crypt.img bs=16M status=progress`
- Создать образ зашифрованного диска


## 🧩 Бонус — практические сценарии

**Создать загрузочную флешку:**
```
dd if=linux.iso of=/dev/sdX bs=4M status=progress
sync
```

**Сделать полный бэкап диска:**
```
dd if=/dev/sda of=backup.img bs=64K conv=noerror,sync status=progress
```

**Проверить скорость записи:**
```
dd if=/dev/zero of=testfile bs=1G count=1 oflag=dsync
```

**Восстановить ISO на диск:**
```
dd if=backup.iso of=/dev/sda bs=4M status=progress
```

**Стереть диск безвозвратно:**
```
dd if=/dev/urandom of=/dev/sdX bs=4M status=progress
```
