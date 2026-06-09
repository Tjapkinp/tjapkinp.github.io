
# 🧭 `whois`: кратко и по делу
`whois` — утилита для запроса **регистрационных данных доменов, IP‑адресов и автономных систем (ASN)** у соответствующих регистратур (TLD‑реестров) и региональных интернет‑регистратур (RIR: ARIN, RIPE, APNIC, LACNIC, AFRINIC).  
Помогает узнать владельца, контактные данные, статусы домена, даты регистрации/истечения, DNS‑серверы, а для IP — сетевой блок, провайдера и зону ответственности (abuse‑контакты).

---

## 📝 Топ‑20 флагов и приёмов `whois` (GNU/RIPE client)
- `whois <domain>` — базовый запрос по домену (реестр будет найден автоматически).
- `whois <ip>` — запрос по IP‑адресу (перенаправит к нужному RIR).
- `whois -h <server> <query>` — указать конкретный whois‑сервер (например, `whois.verisign-grs.com` для .com).
- `whois -p <port> <query>` — нестандартный порт (обычно 43).
- `whois -H <query>` — скрыть юридические дисклеймеры (если поддерживается сервером).
- `whois -v <query>` — подробный/verbose режим клиента (если доступен).
- `whois -l <query>` — «длинный» ответ (на некоторых серверах).
- `whois -r <query>` — отключить рекурсивные запросы (если клиент их делает).
- `whois -a <query>` — запрашивать «всё»/сквозной поиск (зависит от сервера).
- `whois -t <object>` — показать шаблон объекта (актуально для RIPE DB).
- `whois -T <type> <query>` — ограничить типы объектов (RIPE: person, inetnum, route, aut-num).
- `whois -s <sources> <query>` — ограничить источники (напр., `RIPE,APNIC`).
- `whois -i <attr>:<value>` — поиск по атрибуту (RIPE: `-i mnt-by:<mnt>` и т.п.).
- `whois -x <query>` — обратный поиск по связям (если поддерживается).
- `whois --help`, `whois --version` — справка и версия клиента.
- `whois -h whois.iana.org <tld>` — найти реестр TLD (root referral).
- `whois -h whois.verisign-grs.com <domain>` — запрос к .com/.net реестру.
- `whois -h whois.arin.net <ip>` — прямой запрос к ARIN для IPv4/IPv6 в их зоне.
- `whois -h whois.ripe.net <ip|ASxxxx>` — прямой запрос к RIPE (Европа, часть Азии).

> ⚠️ Набор опций частично зависит от реализации клиента и сервера. Если флаг не поддержан, используйте `--help` или смотрите `man whois` своей системы.

---

# 🚀 Айсберг команд `whois` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`whois example.com`
- Базовая информация о домене: регистратор, даты, статусы, DNS

`whois 8.8.8.8`
- Владелец IP‑сети, диапазон, контакты abuse

`whois AS15169`
- Данные по автономной системе (если поддерживает сервер)

`whois -H example.org`
- Скрыть длинные дисклеймеры в ответе

`whois --help`
- Подсказка по опциям клиента


## 🟡 Уровень 2 — Работа с конкретными реестрами

`whois -h whois.verisign-grs.com example.com`
- Запрос напрямую к .com реестру

`whois -h whois.iana.org com`
- Узнать, какой реестр обслуживает TLD `.com`

`whois -h whois.ripe.net 193.0.0.0/8`
- Запрос в RIPE по диапазону

`whois -h whois.arin.net 3.3.3.3`
- Запрос в ARIN по IP

`whois -h whois.ripe.net AS3356`
- Запрос по ASN в RIPE


## 🟠 Уровень 3 — Фильтрация ответа

`whois example.com | egrep -i "Registrar|Registrant|Status|Name Server"`
- Вытянуть ключевые поля домена

`whois 1.1.1.1 | egrep -i "OrgName|country|abuse"`
- Быстро получить владельца, страну и abuse‑контакт

`whois -H example.net | sed -n '/Domain Name:/,/DNSSEC/p'`
- Показать только основной блок записи

`whois -h whois.ripe.net -T inetnum 193.0.0.0/16`
- Попросить только объекты типа `inetnum`

`whois -h whois.ripe.net -i abuse-mailbox cloudflare`
- Поиск по атрибуту в базе RIPE


## 🔵 Уровень 4 — Диагностика доменов

`whois example.com | grep -i "Status"`
- Проверить статусы: clientTransferProhibited, redemptionPeriod и т.д.

`whois example.com | grep -i "Expiry\|Expiration\|paid-till"`
- Дата истечения

`whois example.com | grep -i "Name Server"`
- Какие NS у домена

`whois -h whois.verisign-grs.com example.com | grep -i "Registrar"`
- Определить регистратора

`whois example.com | grep -i "DNSSEC"`
- Проверка DNSSEC-полей


## 🟣 Уровень 5 — IP‑сети и маршруты

`whois 192.0.2.0/24`
- Вытянуть блок и орг‑владельца

`whois -h whois.ripe.net -T route 192.0.2.0/24`
- Посмотреть маршрутные объекты (route/route6)

`whois -h whois.ripe.net -T aut-num AS15169`
- Посмотреть объекты автономной системы

`whois -h whois.arin.net 35.0.0.0`
- ARIN: организация и контакты

`whois -h whois.lacnic.net 201.0.0.0`
- LACNIC: Латинская Америка/Карибы


## 🔴 Уровень 6 — Гуру‑режим

`whois -h whois.ripe.net -s RIPE -B -r 193.0.0.0/8`
- Неотфильтрованный поиск в RIPE (без скрытия данных, если разрешено)

`whois -h whois.ripe.net -r -T person -i nic-hdl:AB12345-RIPE`
- Поиск контактного объекта по NIC‑хэндлу

`whois -h whois.arin.net "n + 8.8.8.0"`
- Специальные запросы ARIN (расширенный формат)

`whois -h whois.ripe.net -t inet6num`
- Показать шаблон объекта (формат полей)

`whois -h whois.apnic.net 1.0.0.0/8`
- Запрос к APNIC (Азиатско‑ТИЖ регион)


## 🟤 Уровень 7 — Автоматизация и отчёты

`for d in $(cat domains.txt); do echo "== $d =="; whois -H $d | egrep -i "Status|Expiry|Registrar"; echo; done`
- Массовая проверка доменов на статус/регистратора/дату

`for ip in $(cat ips.txt); do whois $ip | egrep -i "OrgName|country|abuse"; done`
- Сбор владельцев/стран/abuse по IP‑адресам

`whois example.com | tee whois_example.com.txt`
- Сохранить полный ответ в файл

`awk '{print $1}' domains.txt | xargs -n1 -I{} sh -c 'whois -H {} | grep -i "Expiry"'`
- Скриптовый поиск истечения

`jq -r '.[]' domains.json | xargs -n1 whois`
- Интеграция с JSON‑списком доменов


## 🧩 Бонус — практические сценарии

**Быстро узнать регистратор и дату истечения домена:**
```
whois example.com | egrep -i "Registrar:|Expiry|Expiration|paid-till"
```

**Абуз‑контакт и провайдер для IP:**
```
whois 203.0.113.10 | egrep -i "OrgName|abuse|country"
```

**Уточнить, какой RIR отвечает за IP:**
```
whois -h whois.iana.org 203.0.113.10
```

**Проверить маршрутные объекты в RIPE:**
```
whois -h whois.ripe.net -T route 203.0.113.0/24
```

**Посмотреть NS‑серверы домена:**
```
whois example.com | grep -i "Name Server"
```

> 💡 Советы:
> - Если ответ пустой/скудный — попробуйте **конкретный whois‑сервер** (`-h`) или **другой RIR**.  
> - Поля и формат **разнятся** у разных реестров — подстраивайте `grep/awk`.  
> - Новые доменные зоны (новые gTLD) часто используют специализированные реестры — проверяйте через IANA: `whois -h whois.iana.org <tld>`.
