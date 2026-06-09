
# 🌐 `nslookup`: кратко и по делу
`nslookup` — утилита для **диагностики DNS** и проверки IP-адресов, имён хостов, MX, TXT, PTR, NS и других записей.  
Подходит для отладки проблем с резолвингом, проверки почтовых доменов и анализа DNS-кэшей.

---

## 📝 Топ‑20 ключевых приёмов `nslookup`
- `nslookup example.com` — базовый запрос A/AAAA записи.
- `nslookup 8.8.8.8` — обратный запрос PTR (IP → домен).
- `nslookup example.com 1.1.1.1` — указать конкретный DNS-сервер.
- `nslookup -type=A example.com` — запрос A-записи (IPv4).
- `nslookup -type=AAAA example.com` — запрос AAAA-записи (IPv6).
- `nslookup -type=MX example.com` — проверить почтовые записи.
- `nslookup -type=NS example.com` — показать NS-сервера.
- `nslookup -type=TXT example.com` — показать SPF, DKIM, DMARC.
- `nslookup -type=SOA example.com` — авторитетная информация о зоне.
- `nslookup -type=PTR 8.8.8.8` — обратное разрешение IP → домен.
- `nslookup -query=ANY example.com` — получить все доступные записи.
- `nslookup -timeout=5 example.com` — задать таймаут 5 секунд.
- `nslookup -debug example.com` — расширенный отладочный вывод.
- `nslookup -port=5353 example.com` — запрос через нестандартный порт.
- `nslookup -retry=2 example.com` — повторить запрос 2 раза.
- `nslookup -class=CHAOS version.bind 8.8.8.8` — версия DNS-сервера (BIND).
- `nslookup -debug -type=MX example.com` — отладка почтовых записей.
- `nslookup -vc example.com` — использовать TCP вместо UDP.
- `nslookup -sil example.com` — скрыть неважные строки.
- `nslookup -debug -type=TXT _dmarc.example.com` — проверить DMARC.

---

# 🚀 Айсберг команд `nslookup` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`nslookup example.com`
- Узнать IP-адрес сайта

`nslookup 8.8.8.8`
- Обратный PTR-запрос

`nslookup example.com 1.1.1.1`
- Использовать Cloudflare DNS

`nslookup ya.ru 8.8.8.8`
- Использовать Google DNS

`nslookup github.com`
- Проверка A/AAAA записей


## 🟡 Уровень 2 — Конкретные типы записей

`nslookup -type=A example.com`
- IPv4-адрес

`nslookup -type=AAAA example.com`
- IPv6-адрес

`nslookup -type=MX example.com`
- Почтовые сервера

`nslookup -type=NS example.com`
- Сервера имён

`nslookup -type=TXT example.com`
- Проверка SPF, DKIM, DMARC


## 🟠 Уровень 3 — Отладка и расширенный вывод

`nslookup -debug example.com`
- Подробный вывод пакетов

`nslookup -debug -type=MX example.com`
- Подробности по почтовым серверам

`nslookup -query=ANY example.com`
- Все доступные записи

`nslookup -vc example.com`
- Использовать TCP

`nslookup -timeout=10 example.com`
- Установить таймаут 10 секунд


## 🔵 Уровень 4 — Работа с зонами и серверами

`nslookup -type=SOA example.com`
- Авторитетная запись зоны

`nslookup -type=PTR 8.8.8.8`
- Обратное разрешение

`nslookup -class=CHAOS version.bind 8.8.8.8`
- Узнать версию DNS-сервера

`nslookup -port=5353 example.com`
- Запрос через кастомный порт

`nslookup _dmarc.example.com -type=TXT`
- Проверить DMARC-запись


## 🟣 Уровень 5 — Почтовая инфраструктура

`nslookup -type=MX gmail.com`
- Проверить MX-записи

`nslookup -type=TXT example.com | grep v=spf1`
- Проверка SPF

`nslookup -type=TXT example.com | grep dkim`
- Проверка DKIM

`nslookup -type=TXT _dmarc.example.com`
- Проверка DMARC

`nslookup -query=ANY mail.example.com`
- Проверка всех записей почтового домена


## 🔴 Уровень 6 — Гуру‑режим

`nslookup -debug example.com 8.8.8.8`
- Отладка через конкретный сервер

`nslookup -retry=3 example.com`
- Повторить запрос трижды

`nslookup -debug -type=TXT _dmarc.example.com`
- Отладка DMARC

`nslookup -sil example.com`
- Тихий режим — без «мусорных» строк

`nslookup -vc -debug -type=MX example.com`
- Проверка почтовых записей через TCP


## 🟤 Уровень 7 — Автоматизация и интеграция

`cat domains.txt | xargs -n1 nslookup`
- Проверить список доменов

`for d in $(cat domains.txt); do nslookup $d | grep Address; done`
- Вывести IP всех доменов

`grep "NXDOMAIN" dns.log | awk '{print $5}' | sort | uniq`
- Список неразрешаемых доменов

`dig +short example.com | xargs nslookup`
- Интеграция с `dig`

`nslookup google.com | awk '/^Address:/ {print $2}'`
- Получить только IP


## 🧩 Бонус — практические сценарии

**Быстрая проверка DNS:**
```
nslookup google.com
```

**Посмотреть MX-записи:**
```
nslookup -type=MX example.com
```

**Проверить SPF, DKIM, DMARC:**
```
nslookup -type=TXT example.com
```

**Диагностика с конкретным DNS-сервером:**
```
nslookup example.com 1.1.1.1
```

**Обратное разрешение IP → домен:**
```
nslookup 8.8.8.8
```
