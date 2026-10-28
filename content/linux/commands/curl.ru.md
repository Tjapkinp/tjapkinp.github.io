
`curl` — универсальный инструмент для работы с HTTP, HTTPS, FTP и множеством других протоколов. Подходит для загрузки файлов, отправки форм, REST API, тестирования и отладки сетевых запросов.

## 📝 Топ‑20 самых полезных флагов `curl`
- `-O` — сохранить файл с оригинальным именем.
- `-o file` — сохранить результат под указанным именем.
- `-L` — следовать за редиректами.
- `-I` — показать только заголовки ответа.
- `-s` — тихий режим (без прогресса и ошибок).
- `-S` — показать ошибки даже в тихом режиме.
- `-v` — подробный вывод (заголовки + тело запроса).
- `-k` — игнорировать ошибки SSL.
- `-C -` — продолжить прерванную закачку.
- `-u user:pass` — базовая HTTP-аутентификация.
- `-d data` — отправить данные POST-запросом (`application/x-www-form-urlencoded`).
- `-H "Header: value"` — добавить заголовок запроса.
- `-F "key=@file"` — отправить файл через форму (`multipart/form-data`).
- `-G` — передавать параметры в URL при `-d` (GET вместо POST).
- `-X METHOD` — указать HTTP-метод (`GET`, `POST`, `PUT`, `DELETE`, ...).
- `--limit-rate 200K` — ограничить скорость загрузки/отправки.
- `--compressed` — поддержка сжатого контента.
- `--http2` — использовать HTTP/2 (если сервер поддерживает).
- `--resolve host:port:ip` — принудительно задать IP для домена.
- `--interface eth0` — выбрать сетевой интерфейс для запроса.

---

# 🚀 Айсберг команд `curl` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`curl https://example.com`
- Скачать страницу и вывести её в консоль

`curl -O https://example.com/file.zip`
- Скачать файл с оригинальным именем

`curl -o my.zip https://example.com/file.zip`
- Скачать и сохранить под своим именем

`curl -L https://short.url/123`
- Следовать за редиректами

`curl -I https://example.com`
- Показать только заголовки ответа


## 🟡 Уровень 2 — Загрузка и отправка файлов

`curl -F "file=@/path/to/image.png" https://api.example.com/upload`
- Загрузить файл через форму (multipart)

`curl -F "file1=@a.txt" -F "file2=@b.txt" https://upload.com`
- Отправить несколько файлов

`curl -o result.json https://api.example.com/data`
- Сохранить JSON-ответ

`curl -C - -O https://example.com/big.iso`
- Дозагрузить файл после обрыва

`curl --limit-rate 500K -O https://example.com/large.zip`
- Ограничить скорость загрузки


## 🟠 Уровень 3 — POST, JSON и API

`curl -d "name=Alex&age=25" https://api.example.com/post`
- Отправить POST-данные в формате формы

`curl -d '{"name":"Alex"}' -H "Content-Type: application/json" https://api.example.com/json`
- POST JSON-запрос

`curl -G -d "q=linux&sort=desc" https://api.example.com/search`
- Передача параметров в URL (GET)

`curl -X PUT -d '{"name":"NewName"}' -H "Content-Type: application/json" https://api.example.com/user/1`
- PUT-запрос с JSON-телом

`curl -X DELETE https://api.example.com/user/1`
- DELETE-запрос


## 🔵 Уровень 4 — Авторизация и токены

`curl -u user:pass https://api.example.com/profile`
- Базовая HTTP-аутентификация

`curl -H "Authorization: Bearer TOKEN" https://api.example.com/data`
- Авторизация через Bearer-токен

`curl -H "X-API-Key: 12345" https://api.example.com/stats`
- Кастомный API-ключ

`curl --cert client.pem --key client.key https://secure.example.com`
- Авторизация через клиентский SSL-сертификат

`curl --negotiate -u : https://kerberos.example.com`
- Kerberos-аутентификация


## 🟣 Уровень 5 — Отладка и тестирование

`curl -v https://example.com`
- Подробный вывод запроса и ответа

`curl -s -o /dev/null -w "%{http_code}\n" https://example.com`
- Показать только HTTP-код ответа

`curl --trace trace.txt https://example.com`
- Полный трейс запроса и ответа

`curl --trace-ascii debug.txt https://example.com`
- Текстовый трейс (ASCII)

`curl -vk https://expired.badssl.com`
- Игнорировать SSL-ошибки и показывать детали


## 🔴 Уровень 6 — REST API и автоматизация

`curl https://api.github.com/repos/user/repo | jq '.'`
- Форматированный JSON-вывод с `jq`

`curl -s "https://api.github.com/repos/user/repo" | jq -r '.stargazers_count'`
- Получить число звёзд на GitHub

`curl -X POST -d '{"title":"Bug"}' -H "Authorization: token $TOKEN" https://api.github.com/repos/user/repo/issues`
- Создать issue на GitHub

`curl -s "https://api.example.com/data" | grep "keyword"`
- Фильтрация данных в реальном времени

`for i in {1..5}; do curl -s "https://example.com/page$i" -o page$i.html; done`
- Массовая загрузка страниц


## 🟤 Уровень 7 — Гуру-режим

`curl --resolve mysite.com:443:1.2.3.4 https://mysite.com`
- Принудительно использовать конкретный IP

`curl --interface eth0 https://ifconfig.io`
- Отправить запрос через указанный сетевой интерфейс

`curl -x socks5h://127.0.0.1:9050 https://check.torproject.org`
- Использовать SOCKS5-прокси (Tor)

`curl -k https://selfsigned.local`
- Игнорировать самоподписанные сертификаты

`curl --http2 https://http2.example.com`
- Принудительно использовать HTTP/2

`curl --parallel https://a.com https://b.com https://c.com`
- Загрузить несколько URL одновременно

`curl --retry 5 --retry-delay 2 https://unstable.example.com`
- Повторять запрос при ошибках


## 🧩 Бонус — практические приёмы

**Скачать все PDF-файлы со страницы:**
```
curl -s https://example.com | grep -Eo 'https://[^"]+\.pdf' | xargs -n 1 curl -O
```

**Проверить работоспособность прокси:**
```
curl -x http://proxy:8080 https://ifconfig.me
```

**Отправить cookie вместе с запросом:**
```
curl -b "SESSION=12345" https://example.com
```

**Сохранить cookie в файл и использовать повторно:**
```
curl -c cookies.txt https://example.com
curl -b cookies.txt https://example.com/profile
```

**Проверить заголовки ответа:**
```
curl -I https://example.com
```
