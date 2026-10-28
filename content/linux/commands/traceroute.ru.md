
`traceroute` — утилита для **диагностики маршрутов** прохождения пакетов в сети.  
Отображает каждый узел между вашим устройством и удалённым хостом, замеряет задержки и помогает находить "узкие места".

---

## 📝 Топ‑20 ключевых флагов и приёмов `traceroute`
- `traceroute <host>` — стандартная трассировка по UDP.
- `traceroute -I <host>` — использовать ICMP вместо UDP.
- `traceroute -T <host>` — трассировка через TCP.
- `traceroute -4 <host>` — форсировать IPv4.
- `traceroute -6 <host>` — форсировать IPv6.
- `traceroute -n <host>` — не резолвить DNS, только IP.
- `traceroute -p 443 <host>` — использовать конкретный порт.
- `traceroute -m 20 <host>` — ограничить количество хопов.
- `traceroute -f 5 <host>` — начать с 5-го хопа.
- `traceroute -q 4 <host>` — 4 пакета на каждый хоп.
- `traceroute -w 1 <host>` — таймаут 1 секунда.
- `traceroute -A <host>` — показывать AS-номера.
- `traceroute -g <gateway>` — задать промежуточный шлюз (Loose Source Routing).
- `traceroute -z 50 <host>` — задержка 50 мс между пакетами.
- `traceroute -V` — версия утилиты.
- `traceroute --help` — справка.
- `traceroute -F <host>` — установить флаг "Don't Fragment".
- `traceroute -U <host>` — UDP-трассировка вручную.
- `traceroute -t <TOS> <host>` — установить TOS (тип сервиса).
- `traceroute -r <host>` — не использовать маршрутизаторы, только прямое соединение.

---

# 🚀 Айсберг команд `traceroute` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`traceroute google.com`
- Базовая трассировка

`traceroute -n google.com`
- Только IP-адреса, без DNS

`traceroute -4 google.com`
- Форсировать IPv4

`traceroute -6 google.com`
- Форсировать IPv6

`traceroute -m 15 google.com`
- Ограничить хопы до 15


## 🟡 Уровень 2 — ICMP, TCP и нестандартные порты

`traceroute -I google.com`
- Использовать ICMP вместо UDP

`traceroute -T google.com`
- TCP-трассировка

`traceroute -p 443 google.com`
- Проверка HTTPS-маршрута

`traceroute -p 22 google.com`
- Трассировка SSH

`traceroute -q 5 google.com`
- 5 пакетов на каждый хоп


## 🟠 Уровень 3 — Настройка таймаутов и интервалов

`traceroute -w 1 google.com`
- Таймаут 1 секунда

`traceroute -z 100 google.com`
- Задержка 100 мс между пакетами

`traceroute -q 1 google.com`
- 1 пакет на хоп (ускоренный тест)

`traceroute -q 10 google.com`
- 10 пакетов на хоп для статистики

`traceroute -f 5 google.com`
- Начать трассировку с 5-го хопа


## 🔵 Уровень 4 — Информация о маршрутах и провайдерах

`traceroute -A google.com`
- Показать AS-номера

`traceroute -n -A google.com`
- Только IP + AS

`traceroute -I -A google.com`
- ICMP + AS

`traceroute -T -A google.com`
- TCP + AS

`traceroute --help`
- Встроенная справка


## 🟣 Уровень 5 — Глубокая диагностика

`traceroute -F google.com`
- Установить флаг "Don't Fragment"

`traceroute -U google.com`
- Принудительный UDP-режим

`traceroute -r google.com`
- Игнорировать маршрутизаторы

`traceroute -t 16 google.com`
- Задать TOS

`traceroute -g 192.168.1.1 google.com`
- Указать промежуточный шлюз


## 🔴 Уровень 6 — Гуру‑режим

`traceroute -I -q 10 -w 0.5 google.com`
- ICMP с 10 пакетами и 0.5 сек таймаутом

`traceroute -T -p 443 -q 5 google.com`
- TCP по порту 443, 5 пакетов

`traceroute -m 30 -z 50 google.com`
- Глубокая трассировка до 30 хопов

`traceroute -I -A -q 10 google.com`
- ICMP + AS + 10 пакетов

`traceroute -p 80 -T google.com`
- TCP-трассировка HTTP


## 🟤 Уровень 7 — Автоматизация и интеграция

`traceroute google.com | tee trace.log`
- Сохранить результат

`grep "192.168" trace.log`
- Фильтрация по локальным IP

`traceroute -n google.com | awk '{print $2}'`
- Выводить только IP-адреса

`traceroute -I google.com | grep -E "ms"`
- Фильтрация RTT по ICMP

`traceroute -n google.com | awk '{print $2}' | xargs -n1 ping -c1`
- Пинговать каждый хоп


## 🧩 Бонус — практические сценарии

**Базовая трассировка:**
```
traceroute example.com
```

**ICMP вместо UDP:**
```
traceroute -I example.com
```

**TCP-трассировка HTTPS:**
```
traceroute -T -p 443 example.com
```

**Диагностика IPv6:**
```
traceroute -6 example.com
```

**Сохраняем трассировку в лог:**
```
traceroute google.com | tee trace.log
```
