
# 🛠️ `modprobe`: кратко и по делу
`modprobe` — утилита для **загрузки, выгрузки и управления модулями ядра Linux**.  
В отличие от `insmod` и `rmmod`, сама ищет зависимости и автоматически подключает их при загрузке модуля.

---

## 📝 Топ‑20 команд и приёмов `modprobe`
- `modprobe <module>` — загрузить модуль ядра.
- `modprobe -r <module>` — выгрузить модуль.
- `modprobe --remove-dependencies <module>` — удалить модуль + его зависимости.
- `modprobe --show-depends <module>` — показать зависимости модуля.
- `modprobe -v <module>` — подробный режим, показывает, что грузится.
- `modprobe -q <module>` — тихий режим (без ошибок).
- `modprobe -n <module>` — тестовый запуск, без фактической загрузки.
- `modprobe -f <module>` — форсировать загрузку несовместимого модуля.
- `modprobe -b <module>` — загружать модуль без автозагрузки зависимостей.
- `modprobe -a <module1> <module2>` — загрузить несколько модулей подряд.
- `modprobe -R <module>` — показать, что зависит от этого модуля.
- `modprobe -C /path/to/modprobe.conf <module>` — использовать другой конфиг.
- `modprobe -d /path/to/dir <module>` — загрузить модуль из кастомного каталога.
- `modprobe --showconfig` — показать конфигурацию модулей.
- `modprobe --ignore-install <module>` — игнорировать скрипт установки.
- `modprobe --dry-run <module>` — проверить, что будет загружено.
- `modprobe --syslog <module>` — писать ошибки в syslog вместо stderr.
- `modprobe --verbose <module>` — расширенный вывод загрузки.
- `modprobe --help` — справка.
- `modprobe --version` — версия `kmod`.

---

# 🚀 Айсберг команд `modprobe` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`modprobe btusb`
- Загрузить драйвер Bluetooth

`modprobe -r btusb`
- Выгрузить драйвер Bluetooth

`modprobe --show-depends btusb`
- Показать зависимости модуля

`modprobe -v btusb`
- Подробно показать процесс загрузки

`lsmod | grep btusb`
- Проверить, загружен ли модуль


## 🟡 Уровень 2 — Работа с параметрами

`modinfo btusb`
- Информация о модуле и параметрах

`modprobe btusb disable_ertm=1`
- Передать параметр модулю при загрузке

`echo "options btusb disable_ertm=1" | sudo tee /etc/modprobe.d/btusb.conf`
- Сохранить параметр в конфиге

`sudo modprobe -r btusb && sudo modprobe btusb`
- Перезагрузить модуль с новыми настройками

`grep btusb /etc/modprobe.d/*`
- Проверить, есть ли настройки модуля


## 🟠 Уровень 3 — Автозагрузка и чёрные списки

`echo btusb | sudo tee -a /etc/modules`
- Автоматически загружать модуль при старте

`echo "blacklist btusb" | sudo tee /etc/modprobe.d/blacklist-btusb.conf`
- Заблокировать модуль, чтобы он не загружался

`echo "install btusb /bin/false" | sudo tee /etc/modprobe.d/block-btusb.conf`
- Полностью отключить модуль через установочный хук

`cat /etc/modprobe.d/blacklist.conf | grep btusb`
- Проверить, заблокирован ли модуль

`sudo update-initramfs -u`
- Обновить initramfs после изменения конфигов


## 🔵 Уровень 4 — Отладка и диагностика

`dmesg | grep btusb`
- Проверить ошибки при загрузке

`modprobe -n -v btusb`
- Тестовый запуск без фактической загрузки

`modprobe --dry-run btusb`
- То же самое, что выше

`modprobe -R btusb`
- Показать модули, зависящие от btusb

`journalctl -k | grep btusb`
- Проверить ошибки загрузки через journald


## 🟣 Уровень 5 — Глубокая интеграция

`lsusb | grep -i bluetooth`
- Найти USB-устройство и понять, нужен ли драйвер

`lspci -k`
- Проверить PCI-драйверы и связанные модули

`lsmod | grep btusb`
- Убедиться, что драйвер загружен

`find /lib/modules/$(uname -r) -name "btusb.ko*"`
- Найти бинарник модуля

`modinfo btusb | grep -i firmware`
- Узнать, какие прошивки нужны для драйвера


## 🔴 Уровень 6 — Гуру‑режим

`modprobe -f nvidia`
- Форсированно загрузить драйвер NVIDIA

`modprobe -r nvidia && modprobe nvidia modeset=1`
- Перезагрузить модуль NVIDIA с параметрами

`modprobe -C /tmp/custom.conf vboxdrv`
- Использовать кастомный конфиг

`modprobe -d /custom/kernel/dir vboxdrv`
- Загрузить модуль из альтернативной директории

`modprobe --ignore-install vboxdrv`
- Игнорировать установочные скрипты модуля


## 🟤 Уровень 7 — Автоматизация и массовая загрузка

`modprobe -a snd_hda_intel btusb iwlwifi`
- Загрузить несколько модулей за раз

`lsmod | awk '$3==0 {print $1}' | xargs -n1 modprobe -r`
- Выгрузить все неиспользуемые модули

`grep -R "options" /etc/modprobe.d/`
- Посмотреть все параметры загруженных модулей

`lsmod | awk '{print $1}' | xargs -n1 modinfo | grep -i depends`
- Показать зависимости всех активных модулей

`lsmod | awk '{print $1}' | while read m; do echo "=== $m ==="; modprobe -n $m; done`
- Проверить загрузку всех модулей без их фактической загрузки


## 🧩 Бонус — практические сценарии

**Перезагрузить драйвер Wi-Fi:**
```
sudo modprobe -r iwlwifi && sudo modprobe iwlwifi
```

**Запретить автозагрузку модуля:**
```
echo "blacklist nouveau" | sudo tee /etc/modprobe.d/blacklist-nouveau.conf
```

**Включить поддержку AHCI вручную:**
```
sudo modprobe ahci
```

**Загрузить драйвер GPU и проверить:**
```
sudo modprobe nvidia
lsmod | grep nvidia
```

**Диагностика при проблемах с Bluetooth:**
```
dmesg | grep btusb
modprobe -r btusb && modprobe btusb
```
