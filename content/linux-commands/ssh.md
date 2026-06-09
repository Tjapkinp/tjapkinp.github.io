
`ssh` — безопасный удалённый доступ к серверам и туннелям: подключайся, проксируй порты (L/R/D), прыгай через бастионы и автоматизируй деплой.

## 📝 Топ‑20 самых полезных флагов `ssh`
- `-p PORT` — порт сервера (по умолчанию 22).
- `-i KEY` — приватный ключ (identity file).
- `-J HOST[,HOST...]` — jump‑host(ы), прокси‑прыжок (замена ProxyCommand).
- `-L [bind:]LPORT:HOST:RPORT` — локальный порт‑форвардинг.
- `-R [bind:]RPORT:HOST:LPORT` — удалённый порт‑форвардинг.
- `-D [bind:]PORT` — динамический SOCKS‑прокси.
- `-N` — не запускать удалённую команду (для туннелей).
- `-f` — уйти в фон после аутентификации (с `-N` для туннелей).
- `-t` / `-tt` — насильно выделить TTY (для `sudo`, `top`, `htop`).
- `-T` — не выделять TTY (ускоряет non‑interactive команды).
- `-A` — проброс ssh‑агента (agent forwarding).
- `-o KEY=VAL` — задать любую опцию (`UserKnownHostsFile`, `StrictHostKeyChecking=no`, `ServerAliveInterval=60`, и т.д.).
- `-C` — сжатие трафика.
- `-4` / `-6` — использовать IPv4 / IPv6.
- `-q` — тихий режим, меньше чата.
- `-v` / `-vv` / `-vvv` — подробный отладочный вывод.
- `-F FILE` — альтернативный ssh‑конфиг.
- `-K` — GSSAPI аутентификация (Kerberos, если настроено).
- `-g` — разрешить внешние подключения к локально форвардному порту (`-L`, `-D`).

---

# 🚀 Айсберг команд `ssh` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`ssh user@host`
- Подключиться по умолчанию (порт 22)

`ssh -p 2222 user@host`
- Подключиться на нестандартный порт

`ssh -i ~/.ssh/id_ed25519 user@host`
- Использовать конкретный приватный ключ

`ssh user@host 'uptime && df -h'`
- Выполнить удалённую команду и выйти

`ssh-copy-id -i ~/.ssh/id_ed25519.pub user@host`
- Скопировать публичный ключ на сервер (включить вход по ключу)


## 🟡 Уровень 2 — Удобство и живучесть сессий

`ssh -o ServerAliveInterval=60 -o ServerAliveCountMax=3 user@host`
- Поддерживать соединение и корректно падать при обрывах

`ssh -o StrictHostKeyChecking=accept-new user@host`
- Автоматически принимать новые host keys (безопаснее, чем `no`)

`ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no user@host`
- Игнорировать known_hosts (только для test-окружений!)

`ssh -t user@host 'sudo -i'`
- Насильно выделить TTY для `sudo`/интерактива

`ssh -T git@github.com`
- Без TTY: удобно для автоматизации/CI


## 🟠 Уровень 3 — Тонкие настройки через `-o` и конфиги

`ssh -o ConnectTimeout=5 -o Compression=yes user@host`
- Таймаут коннекта и сжатие

`ssh -o IdentityAgent=~/.ssh/agent.sock user@host`
- Указать путь к ssh‑агенту

`ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes user@host`
- Явно использовать вход по ключу

`ssh -F ./ssh_config user@host`
- Использовать альтернативный конфиг

`# ~/.ssh/config (пример хостов)`
- `Host bastion`
- `  HostName bastion.example.com`
- `  User ops`
- `Host app`
- `  HostName app.internal`
- `  User deploy`
- `  ProxyJump bastion`
- `  IdentityFile ~/.ssh/id_ed25519`


## 🔵 Уровень 4 — Туннели и прокси

`ssh -L 127.0.0.1:5432:db.internal:5432 user@bastion`
- Локальный форвард PostgreSQL через бастион

`ssh -R 8080:localhost:3000 user@host`
- Удалённый форвард: сервис на локальном 3000 доступен на сервере по 8080

`ssh -D 1080 user@host`
- SOCKS5‑прокси на 1080; в браузере укажи SOCKS `127.0.0.1:1080`

`ssh -N -f -L 8443:127.0.0.1:443 user@host`
- Фоновый локальный туннель (без команд)

`ssh -g -L 0.0.0.0:8022:127.0.0.1:22 user@host`
- Открыть локальный порт для внешних клиентов (осторожно!)


## 🟣 Уровень 5 — Бастионы и цепочки

`ssh -J bastion user@app.internal`
- Подключиться к app через бастион (ProxyJump)

`ssh -J bastion1,bastion2 user@deep.internal`
- Многоступенчатый прыжок

`ssh -o ProxyCommand='nc -X 5 -x 127.0.0.1:1080 %h %p' user@host`
- ProxyCommand через SOCKS5 (альтернатива `-J`)

`ssh -A -J bastion git@github.com`
- Агент‑форвардинг через бастион (для `git clone` с закрытого сегмента)

`ssh -W target:22 bastion`
- Использовать сервер как простую TCP‑прокладку (ssh `-W`)


## 🔴 Уровень 6 — Мультиплексирование и скорость

`ssh -o ControlMaster=auto -o ControlPath=~/.ssh/cm-%r@%h:%p -o ControlPersist=10m user@host`
- Переиспользовать одно TCP‑соединение для многих сессий (молниеносные повторы)

`ssh -S ~/.ssh/cm-user@host:22 -O check user@host`
- Управлять существующим мастером (check/exit/stop)

`rsync -az -e "ssh -o ControlPath=~/.ssh/cm-%r@%h:%p" ./dir user@host:/srv/dir`
- Быстрый `rsync` поверх открытого multiplex‑канала

`GIT_SSH_COMMAND='ssh -o ControlPath=~/.ssh/cm-%r@%h:%p' git fetch --all`
- Ускорить Git поверх уже установленного канала


## 🟤 Уровень 7 — Безопасность и продвинутое

`ssh-keygen -t ed25519 -C "you@host"`
- Сгенерировать современную пару ключей

`ssh-keygen -A`
- Сгенерировать host keys на сервере (если отсутствуют)

`ssh-keygen -R host && ssh-keyscan -H host >> ~/.ssh/known_hosts`
- Обновить устаревший host key

`ssh -Q key` / `ssh -Q cipher` / `ssh -Q kex`
- Показать поддерживаемые ключи/шифры/алгоритмы обмена ключами

`ssh -o KexAlgorithms=... -o Ciphers=... user@host`
- Зафиксировать алгоритмы (совместимость/безопасность)

`ssh -o PermitLocalCommand=yes -o LocalCommand='echo connected: %n' user@host`
- Выполнить локальную команду после соединения


## 🧩 Бонус — scp/sftp/rsync поверх SSH

`scp -P 2222 file.txt user@host:/tmp/`
- Быстрая передача файла (порт 2222)

`scp -r ./dir user@host:/data/`
- Рекурсивная загрузка директории

`sftp -P 2222 user@host`
- Интерактивный SFTP‑клиент

`rsync -avz -e "ssh -p 2222" ./local/ user@host:/remote/`
- Эффективная синхронизация поверх SSH (повторные передачи — инкрементальные)

`ssh user@host 'tar -czf - /var/www' | tar -xzf - -C ./backup`
- Копирование большого дерева без временных файлов (стрим через SSH)

