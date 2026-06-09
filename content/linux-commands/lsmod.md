
# 🧩 `lsmod`: кратко и по делу
`lsmod` — утилита для отображения списка **загруженных модулей ядра** Linux.  
Модули ядра — это драйверы и расширения, которые могут быть динамически загружены или выгружены без перезагрузки системы.

---

## 📝 Топ‑20 команд и приёмов для работы с модулями ядра

### Основные команды
- `lsmod` — список всех загруженных модулей.
- `lsmod | grep <module>` — проверить, загружен ли модуль.
- `modprobe <module>` — загрузить модуль ядра.
- `modprobe -r <module>` — выгрузить модуль.
- `modprobe --show-depends <module>` — показать зависимости модуля.
- `modinfo <module>` — полная информация о модуле.
- `insmod <file.ko>` — загрузить модуль напрямую из файла.
- `rmmod <module>` — удалить модуль ядра.
- `dmesg | grep <module>` — проверить логи загрузки/ошибок.
- `ls /lib/modules/$(uname -r)/kernel/` — директория с модулями ядра.

### Фильтрация и диагностика
- `lsmod | sort` — отсортировать список модулей.
- `lsmod | column -t` — красиво выровнять таблицу.
- `lsmod | grep usb` — проверить USB-драйверы.
- `lsmod | grep nvidia` — проверить драйвер NVIDIA.
- `lsmod | awk '{print $1, $2}'` — вывести модули и их размер.
- `lsmod | grep ath9k` — проверить Wi-Fi драйвер Atheros.
- `lsmod | grep snd` — проверить аудиодрайверы.
- `find /lib/modules/$(uname -r) -name "*.ko"` — найти все модули ядра.
- `modinfo vboxdrv` — узнать версию драйвера VirtualBox.
- `modinfo nvidia | grep -i version` — узнать версию драйвера NVIDIA.

---

# 🚀 Айсберг команд `lsmod` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`lsmod`
- Список всех загруженных модулей

`lsmod | grep usb`
- Проверить USB-драйверы

`lsmod | grep nvidia`
- Проверить NVIDIA-драйвер

`lsmod | wc -l`
- Количество загруженных модулей

`lsmod | head`
- Посмотреть первые строки списка


## 🟡 Уровень 2 — Работа с драйверами

`modprobe vboxdrv`
- Загрузить модуль VirtualBox

`modprobe -r vboxdrv`
- Выгрузить модуль VirtualBox

`modprobe --show-depends vboxdrv`
- Посмотреть зависимости модуля

`modinfo vboxdrv`
- Информация о модуле VirtualBox

`modinfo nvidia | grep filename`
- Узнать, где лежит драйвер NVIDIA


## 🟠 Уровень 3 — Управление модулями вручную

`sudo insmod ./mymodule.ko`
- Загрузить модуль напрямую из файла

`sudo rmmod mymodule`
- Удалить модуль ядра

`sudo modprobe mymodule param=1`
- Загрузить модуль с параметрами

`sudo dmesg | tail -n 20`
- Проверить результат загрузки

`find /lib/modules/$(uname -r) -name "*.ko" | grep mymodule`
- Найти бинарник модуля


## 🔵 Уровень 4 — Глубокая диагностика и интеграция

`lsmod | grep snd`
- Проверить аудиодрайверы

`lsmod | grep ath`
- Проверить Wi-Fi-драйверы

`lsusb`
- Посмотреть устройства на USB и сверить модули

`lspci -k`
- Проверить, какой драйвер закреплён за PCI-устройством

`dmesg | grep -i "failed\|error"`
- Поиск ошибок загрузки модулей


## 🟣 Уровень 5 — Оптимизация и безопасность

`lsmod | awk '{print $1, $3}' | sort -k2 -n`
- Посмотреть модули, которыми пользуется больше всего процессов

`lsmod | awk '$3==0 {print $1}'`
- Список неиспользуемых модулей

`modprobe -r $(lsmod | awk '$3==0 {print $1}')`
- Выгрузить все неиспользуемые модули

`lsmod | grep bluetooth`
- Проверить BT-модуль

`sudo modprobe -r btusb`
- Выгрузить драйвер Bluetooth


## 🔴 Уровень 6 — Гуру‑режим

`lsmod | grep vfio`
- Проверить модули для PCI-пасстру через KVM/QEMU

`lsmod | grep kvm`
- Проверить модули виртуализации

`lsmod | grep dm_mod`
- Проверить драйвер LVM

`lsmod | grep btrfs`
- Проверить поддержку Btrfs

`sudo modprobe vfio-pci`
- Подключить VFIO для passthrough


## 🟤 Уровень 7 — Автоматизация и отладка

`for m in $(lsmod | awk '{print $1}' | tail -n +2); do echo "=== $m ==="; modinfo $m | grep description; done`
- Вывести описание всех модулей

`lsmod | awk '{print $1}' | while read m; do modinfo $m | grep -i filename; done`
- Показать пути всех модулей

`lsmod | awk '{print $1}' | xargs -n1 modinfo | grep -i author`
- Найти авторов всех модулей

`lsmod | grep -v Module | awk '{print $1}' | xargs -n1 modinfo | grep depends`
- Показать зависимости всех модулей

`find /sys/module/ -maxdepth 1 -type d`
- Список всех активных модулей ядра


## 🧩 Бонус — практические сценарии

**Проверить драйвер Wi-Fi:**
```
lspci -k | grep -A3 Network
```

**Найти, какой модуль использует устройство:**
```
lsusb -v | grep -i driver
```

**Посмотреть ошибки загрузки модулей:**
```
dmesg | grep -i "module"
```

**Загрузить модуль вручную и проверить:**
```
sudo modprobe btusb
lsmod | grep btusb
```

**Удалить неиспользуемые модули:**
```
sudo modprobe -r $(lsmod | awk '$3==0 {print $1}')
```
