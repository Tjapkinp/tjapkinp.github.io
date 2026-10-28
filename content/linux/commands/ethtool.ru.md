
# 🌐 `ethtool`: кратко и по делу
`ethtool` — утилита для **диагностики, настройки и тестирования сетевых интерфейсов** в Linux.  
Позволяет проверять скорость, дуплекс, статистику ошибок, поддержку Wake-on-LAN, управление энергопотреблением и многое другое.

---

## 📝 Топ‑20 ключевых флагов и приёмов `ethtool`
- `ethtool eth0` — подробная информация об интерфейсе.
- `ethtool -i eth0` — версия драйвера и прошивки.
- `ethtool -S eth0` — расширенная статистика интерфейса.
- `ethtool -g eth0` — размер RX/TX буферов.
- `ethtool -k eth0` — текущие offload-настройки.
- `ethtool -K eth0 tso off` — выключить TCP Segmentation Offload.
- `ethtool -K eth0 gso off gro off` — отключить GSO и GRO.
- `ethtool -p eth0 5` — мигнуть светодиодом на порту 5 секунд.
- `ethtool -r eth0` — перезапустить линк без ifdown.
- `ethtool -s eth0 speed 100 duplex full autoneg off` — зафиксировать скорость и дуплекс.
- `ethtool -s eth0 autoneg on` — включить автосогласование.
- `ethtool -n eth0 rx-flow-hash tcp4` — алгоритм распределения пакетов.
- `ethtool -c eth0` — показать параметры коалесцирования IRQ.
- `ethtool -C eth0 rx-usecs 50` — изменить настройки IRQ.
- `ethtool -P eth0` — показать MAC-адрес.
- `ethtool -T eth0` — поддерживаемые таймстемпы.
- `ethtool -L eth0 combined 4` — изменить количество очередей RX/TX.
- `ethtool -m eth0` — показать данные SFP-модуля (если доступно).
- `ethtool -t eth0 online` — тестировать интерфейс в реальном времени.
- `ethtool -a eth0` — параметры управления потоком (pause frames).

---

# 🚀 Айсберг команд `ethtool` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`ethtool eth0`
- Общая информация о интерфейсе

`ethtool -i eth0`
- Версия драйвера, прошивки и bus-info

`ethtool -S eth0`
- Основная статистика интерфейса

`ethtool -P eth0`
- Показать MAC-адрес

`ethtool -p eth0 3`
- Подсветить порт 3 секунды


## 🟡 Уровень 2 — Управление скоростью и дуплексом

`ethtool eth0 | grep Speed`
- Узнать текущую скорость

`ethtool -s eth0 speed 100 duplex full`
- Зафиксировать скорость 100 Мбит/с

`ethtool -s eth0 autoneg on`
- Включить автонастройку

`ethtool -s eth0 speed 1000 duplex full autoneg off`
- Принудительно выставить гигабитное соединение

`ethtool -r eth0`
- Перезапуск линка без перезагрузки интерфейса


## 🟠 Уровень 3 — Управление offload-настройками

`ethtool -k eth0`
- Список текущих offload-опций

`ethtool -K eth0 tso off`
- Выключить TCP segmentation offload

`ethtool -K eth0 gso off gro off`
- Выключить GSO и GRO

`ethtool -K eth0 rx off tx off`
- Выключить аппаратные offload-ускорения

`ethtool -K eth0 lro on`
- Включить Large Receive Offload


## 🔵 Уровень 4 — Статистика и тестирование

`ethtool -S eth0`
- Подробная статистика

`ethtool -t eth0`
- Запуск встроенного теста линка

`ethtool -m eth0`
- Информация о SFP-модуле

`ethtool -g eth0`
- Размер RX/TX буферов

`ethtool -T eth0`
- Поддержка timestamping


## 🟣 Уровень 5 — IRQ, очереди и производительность

`ethtool -c eth0`
- Текущие IRQ-настройки

`ethtool -C eth0 rx-usecs 100`
- Изменить задержку прерываний

`ethtool -L eth0 combined 4`
- Изменить количество RX/TX-очередей

`ethtool -n eth0 rx-flow-hash tcp4`
- Хеширование потоков TCP

`ethtool -a eth0`
- Управление pause frames (flow control)


## 🔴 Уровень 6 — Гуру‑режим

`ethtool -x eth0`
- Показать RSS-хэш и распределение ядер

`ethtool -X eth0 equal 4`
- Настроить RSS для 4 ядер

`ethtool -N eth0 rx-flow-hash udp4 sdfn`
- Управление хешированием UDP

`ethtool -w eth0`
- Сохранить EEPROM-драйвера (если доступно)

`ethtool -G eth0 rx 1024 tx 1024`
- Изменить глубину RX/TX буферов


## 🟤 Уровень 7 — Автоматизация и интеграция

`watch -n1 "ethtool eth0 | grep Speed"`
- Автоматический мониторинг скорости

`ethtool eth0 | tee eth0_status.log`
- Логировать состояние интерфейса

`grep -i "Link detected" eth0_status.log`
- Проверить, был ли линк

`iperf3 -c 192.168.1.10 -P 4`
- Измерить скорость после изменения настроек

`ss -tup | grep :80`
- Проверить активные соединения после оптимизации


## 🧩 Бонус — практические сценарии

**Проверить скорость линка:**
```
ethtool eth0 | grep Speed
```

**Выключить аппаратные offload'ы для tcpdump:**
```
sudo ethtool -K eth0 gro off gso off tso off
```

**Зафиксировать скорость и дуплекс:**
```
sudo ethtool -s eth0 speed 1000 duplex full autoneg off
```

**Проверить количество ошибок:**
```
ethtool -S eth0 | grep -i error
```

**Включить подсветку порта для поиска кабеля:**
```
sudo ethtool -p eth0 5
```
