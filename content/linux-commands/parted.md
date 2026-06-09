
`parted` — мощный инструмент для работы с дисками и разделами.  
Позволяет создавать, изменять, удалять, перемещать и проверять разделы, управлять таблицами разделов (MBR/GPT) и форматировать устройства.

## 📝 Топ‑20 полезных команд и флагов `parted`
- `parted` — запустить интерактивный режим.
- `parted /dev/sdX` — открыть конкретный диск.
- `print` — показать таблицу разделов.
- `mklabel gpt` — создать новую GPT таблицу разделов.
- `mklabel msdos` — создать MBR таблицу разделов.
- `mkpart primary ext4 1MiB 100GiB` — создать раздел.
- `mkpart logical fat32 100GiB 150GiB` — создать логический раздел.
- `rm <N>` — удалить раздел с номером N.
- `resizepart <N> END` — изменить размер раздела.
- `name <N> LABEL` — задать имя раздела.
- `align-check optimal <N>` — проверить выравнивание раздела.
- `set <N> boot on` — сделать раздел загрузочным.
- `set <N> lvm on` — включить LVM на разделе.
- `set <N> raid on` — отметить раздел как RAID.
- `set <N> swap on` — пометить раздел как swap.
- `unit s` — переключить единицы измерения в сектора.
- `unit MiB` — измерения в мегабайтах.
- `unit GiB` — измерения в гигабайтах.
- `help` — показать список всех команд.
- `quit` — выйти из parted.

---

# 🚀 Айсберг команд `parted` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`sudo parted /dev/sda`
- Открыть основной диск

`(parted) print`
- Показать таблицу разделов

`(parted) mklabel gpt`
- Создать новую GPT таблицу

`(parted) mkpart primary ext4 1MiB 100GiB`
- Создать раздел ext4

`(parted) quit`
- Выйти из parted


## 🟡 Уровень 2 — Управление разделами

`(parted) mkpart logical linux-swap 100GiB 120GiB`
- Создать swap-раздел

`(parted) name 2 "DATA"`
- Задать имя второго раздела

`(parted) rm 3`
- Удалить раздел №3

`(parted) resizepart 1 150GiB`
- Увеличить раздел до 150 ГБ

`(parted) set 1 boot on`
- Сделать раздел загрузочным


## 🟠 Уровень 3 — Тонкая настройка и форматирование

`(parted) unit MiB`
- Переключить отображение размеров в MiB

`(parted) unit s`
- Показать размеры в секторах

`(parted) align-check optimal 1`
- Проверить выравнивание раздела №1

`mkfs.ext4 /dev/sda1`
- Форматировать раздел под ext4

`mkfs.vfat -F32 /dev/sda2`
- Форматировать раздел под FAT32


## 🔵 Уровень 4 — Проверка и диагностика

`(parted) print free`
- Показать незанятое пространство на диске

`(parted) rescue START END`
- Восстановить повреждённый раздел

`(parted) check 2`
- Проверить второй раздел на ошибки

`lsblk -f`
- Альтернатива parted для проверки разделов

`blkid`
- Узнать UUID всех разделов


## 🟣 Уровень 5 — Работа с GPT и MBR

`(parted) mklabel msdos`
- Создать MBR таблицу

`(parted) mklabel gpt`
- Создать GPT таблицу

`(parted) set 2 lvm on`
- Включить LVM для второго раздела

`(parted) set 3 raid on`
- Отметить раздел как RAID

`gdisk /dev/sda`
- Более продвинутый аналог parted для GPT


## 🔴 Уровень 6 — Гуру‑режим

`parted -s /dev/sdb mklabel gpt mkpart primary ext4 1MiB 100%`
- Создать GPT-диск и раздел на весь диск

`parted -s /dev/sdc mkpart primary 1MiB 51200MiB`
- Автоматически создать раздел

`parted -s /dev/sdd mklabel msdos mkpart primary fat32 1MiB 100%`
- Создать MBR-диск и FAT32 раздел

`parted -s /dev/sdb print | grep ext4`
- Найти все ext4-разделы

`sfdisk -d /dev/sda > table.txt`
- Сохранить схему разделов в файл


## 🧩 Бонус — практические сценарии

**Создать GPT-диск и раздел:**
```
parted /dev/sda
mklabel gpt
mkpart primary ext4 1MiB 100%
quit
```

**Создать загрузочную флешку:**
```
parted /dev/sdb mklabel msdos
parted /dev/sdb mkpart primary fat32 1MiB 100%
mkfs.vfat -F32 /dev/sdb1
```

**Восстановить раздел после повреждения:**
```
parted /dev/sda rescue 1MiB 500GiB
```

**Сделать раздел LVM:**
```
parted /dev/sda set 2 lvm on
```

**Проверить свободное место:**
```
parted /dev/sda print free
```
