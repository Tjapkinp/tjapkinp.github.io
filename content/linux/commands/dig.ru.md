
# 🌐 `dig`: кратко и по делу
`dig` (Domain Information Groper) — мощная утилита для **DNS-запросов и диагностики**.  
Позволяет проверять A, AAAA, MX, NS, CNAME, TXT, PTR-записи, трассировать DNS-резолвинг и отлаживать проблемы с именами доменов.

---

## 📝 Топ‑20 ключевых флагов и приёмов `dig`
- `dig <domain>` — стандартный запрос A-записи.
- `dig <domain> A` — явно указать запрос A-записи.
- `dig <domain> AAAA` — получить IPv6.
- `dig <domain> MX` — почтовые сервера.
- `dig <domain> NS` — DNS-сервера.
- `dig <domain> TXT` — TXT-записи (SPF, DKIM, DMARC).
- `dig <domain> CNAME` — алиасы доменов.
- `dig <domain> SOA` — информация о зоне.
- `dig -x <IP>` — обратный PTR-запрос.
- `dig +short <domain>` — короткий вывод.
- `dig +trace <domain>` — трассировка DNS-резолвинга.
- `dig @8.8.8.8 <domain>` — использовать конкретный DNS-сервер.
- `dig +noall +answer <domain>` — показать только ответ.
- `dig +stats <domain>` — показать статистику запроса.
- `dig +tcp <domain>` — использовать TCP вместо UDP.
- `dig +multiline <domain>` — красивый многострочный вывод.
- `dig ANY <domain>` — получить все доступные записи.
- `dig <domain> -p 5353` — указать нестандартный DNS-порт.
- `dig -f domains.txt` — массовые DNS-запросы.
- `dig +dnssec <domain>` — запросить DNSSEC-записи.

---

# 🚀 Айсберг команд `dig` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`dig google.com`
- Получить A-записи

`dig +short google.com`
- Только IP-адрес

`dig -x 8.8.8.8`
- Обратный PTR-запрос

`dig google.com MX`
- Узнать почтовые сервера

`dig google.com NS`
- DNS-сервера домена


## 🟡 Уровень 2 — TXT, SPF, DKIM, DMARC

`dig google.com TXT`
- Все TXT-записи

`dig gmail.com TXT | grep spf`
- Проверить SPF-записи

`dig _dmarc.google.com TXT`
- Проверить DMARC-записи

`dig google.com TXT | grep "v=DKIM"`
- Проверить DKIM

`dig example.com TXT +short`
- Сокращённый вывод TXT-записей


## 🟠 Уровень 3 — Отладка DNS и трассировка

`dig +trace example.com`
- Пошаговый путь от корневых DNS

`dig @1.1.1.1 example.com`
- Использовать Cloudflare DNS

`dig @8.8.8.8 example.com`
- Использовать Google DNS

`dig +tcp example.com`
- Форсировать TCP-запрос

`dig +time=2 example.com`
- Установить таймаут ответа


## 🔵 Уровень 4 — Работа с DNS-зонами

`dig example.com SOA`
- Проверить SOA-запись

`dig example.com ANY`
- Все доступные записи

`dig +noall +answer example.com`
- Показать только результат

`dig -p 5353 example.com`
- Использовать нестандартный порт DNS

`dig +stats example.com`
- Показать время ответа и статистику


## 🟣 Уровень 5 — Массовые DNS-запросы

`dig -f domains.txt`
- Запросить A-записи для списка доменов

`dig +short -f domains.txt`
- Короткий вывод IP для всех доменов

`for d in $(cat domains.txt); do dig +short $d; done`
- Альтернативный способ массовой проверки

`dig +time=1 +retry=1 -f domains.txt`
- Ускоренный режим запросов

`xargs -n1 dig +short < domains.txt`
- Через `xargs` для ускорения


## 🔴 Уровень 6 — Диагностика DNSSEC

`dig +dnssec example.com`
- Проверить DNSSEC-подписи

`dig example.com DNSKEY +multiline`
- Проверить ключи зоны

`dig +trace +dnssec example.com`
- Полная трассировка с DNSSEC

`dig example.com DS`
- Запрос DS-записей

`dig example.com RRSIG`
- Проверить цифровые подписи


## 🟤 Уровень 7 — Гуру‑режим

`dig +short myip.opendns.com @resolver1.opendns.com`
- Узнать свой публичный IP

`dig -x 1.1.1.1 +short`
- Обратное PTR-имя

`dig @9.9.9.9 google.com +short`
- Использовать Quad9 DNS

`dig example.com +trace +time=1 +retry=1`
- Быстрая трассировка

`dig +tcp @1.1.1.1 example.com +stats`
- Принудительно через TCP + показать статистику


## 🧩 Бонус — практические сценарии

**Проверить IP домена:**
```
dig +short example.com
```

**Найти CNAME:**
```
dig example.com CNAME
```

**Проверить DKIM и DMARC:**
```
dig default._domainkey.example.com TXT
dig _dmarc.example.com TXT
```

**Трассировка DNS:**
```
dig +trace example.com
```

**Проверить все записи домена:**
```
dig ANY example.com +noall +answer
```
