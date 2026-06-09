
`rsyslog` — высокопроизводительный демон системных логов (замена/наследник syslogd), поддерживает фильтры, шаблоны, очереди, сеть (UDP/TCP/TLS/RELP), интеграцию с journald и гибкую маршрутизацию по файлам/сокетам/удалённым серверам.

- Конфиги: `/etc/rsyslog.conf` (общий) и каталоги включений `/etc/rsyslog.d/*.conf`  
- Сервис: `systemctl (re)start rsyslog && systemctl status rsyslog`  
- Тест: `logger "hello from rsyslog"` (пишет в `user` facility по умолчанию)

## 📝 Топ‑20 ключевых модулей, опций и приёмов
- `module(load="imuxsock")` — вход локальных сообщений (unix‑сокет `/dev/log`).
- `module(load="imjournal")` — чтение из systemd‑журнала (journald).
- `module(load="imklog")` — сообщения ядра (klog).
- `module(load="imudp")` / `input(type="imudp" port="514")` — UDP‑вход.
- `module(load="imtcp")` / `input(type="imtcp" port="514")` — TCP‑вход.
- `module(load="imrelp")` / `input(type="imrelp" port="20514")` — надёжный RELP‑вход.
- `action(type="omfile" file="/var/log/app.log")` — запись в файл.
- `action(type="omfwd" target="log.example.com" protocol="tcp" port="514")` — форвардинг UDP/TCP.
- `action(type="omrelp" target="log.example.com" port="20514")` — форвардинг по RELP.
- `template(name="T" type="string" string="%timegenerated% %HOSTNAME% %syslogtag% %msg%\n")` — настраиваемый формат.
- Селекторы RFC3164/RFC5424: `facility.level` (например, `authpriv.*`).
- Свойственные фильтры: `if $programname == "nginx" then ...`.
- Rate limiting: `module(load="imudp" RateLimit.Interval="0")` (выключить) / либо установить интервалы.
- Очереди действий: `queue.type="LinkedList" queue.size="10000"` — буфер на диск/в память.
- TLS для TCP: `streamDriver="gtls" streamDriverMode="1" streamDriverAuthMode="x509/name"`.
- Динамические пути: `template(name="Path" type="string" string="/var/log/apps/%hostname%/app.log")`.
- Правила наборами (rulesets): `ruleset(name="R1") { ... }` и привязка входа `input(... ruleset="R1")`.
- Включения: `include(file="/etc/rsyslog.d/*.conf" mode="optional")` (обычно задано в главном конфиге).
- Отладка: `rsyslogd -N1` (проверка синтаксиса), `rsyslogd -dn` (debug, не демонизироваться).

---

# 🚀 Айсберг по `rsyslog` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`sudo systemctl status rsyslog`
- Проверить состояние демона

`echo "*.* /var/log/all.log" | sudo tee /etc/rsyslog.d/00-all.conf && sudo systemctl restart rsyslog`
- Писать все сообщения в один файл (правило селектора)

`logger "hello world"`
- Отправить тестовое сообщение

`logger -p authpriv.warning "login attempt failed"`
- Указать facility/priority для теста

`tail -f /var/log/syslog` *(Debian/Ubuntu)* / `tail -f /var/log/messages` *(RHEL/CentOS)*
- Смотреть поток системных логов


## 🟡 Уровень 2 — Классические селекторы и файлы

`authpriv.*  -/var/log/secure`
- Все auth‑сообщения → `secure` (с предзнаком `-` для no‑sync)

`mail.info   /var/log/mail.info`
- Писать почтовые инфо‑события

`*.emerg     :omusrmsg:*`
- Срочные сообщения всем залогиненным пользователям

`cron.*      /var/log/cron.log`
- Логи `cron` отдельно

`kern.warning /var/log/kern.warn`
- Предупреждения ядра в свой файл


## 🟠 Уровень 3 — Современный синтаксис RainerScript (property‑based)

```
if ($programname == "nginx" and $syslogseverity <= 5) then {
  action(type="omfile" file="/var/log/nginx/filtered.log")
} else {
  stop
}
```
- Фильтрация по имени программы и уровню

```
if ($msg contains "timeout") then action(type="omfile" file="/var/log/timeouts.log")
```
- Контекстный фильтр по содержимому

```
if ($hostname == "app-01") then action(type="omrelp" target="siem" port="20514")
```
- Точное правило маршрутизации по хосту


## 🔵 Уровень 4 — Получение логов из сети

```
module(load="imudp")
input(type="imudp" port="514")

module(load="imtcp")
input(type="imtcp" port="514")
```
- Поднять UDP/TCP‑приём на 514

```
template(name="NetFmt" type="string" string="<%pri%>%timestamp:::date-rfc3339% %HOSTNAME% %syslogtag%%msg%\n")
if ($fromhost-ip != "127.0.0.1") then action(type="omfile" file="/var/log/remote.log" template="NetFmt")
```
- Формат и сбор удалённых логов в отдельный файл

```
module(load="imrelp")
input(type="imrelp" port="20514")
```
- Надёжный вход RELP (без потерь при обрывах)


## 🟣 Уровень 5 — Форвардинг (UDP/TCP/TLS/RELP)

```
action(type="omfwd" target="log.example.com" port="514" protocol="udp")
```
- Простой UDP форвардинг

```
action(type="omfwd" target="log.example.com" port="6514" protocol="tcp"
       StreamDriver="gtls" StreamDriverMode="1" StreamDriverAuthMode="x509/name"
       tls.cacert="/etc/rsyslog.d/ca.pem" )
```
- Безопасный TCP+TLS форвардинг (порт 6514 по стандарту)

```
action(type="omrelp" target="log.example.com" port="20514")
```
- Форвардинг по RELP (надёжная доставка)


## 🔴 Уровень 6 — Шаблоны, динамические пути и очереди

```
template(name="PerHostPath" type="string" string="/var/log/hosts/%hostname%/app.log")
if ($programname == "myapp") then action(type="omfile" dynaFile="PerHostPath")
```
- Писать по отдельному файлу на каждый хост

```
action(type="omfwd" target="log.example.com" protocol="tcp" queue.type="LinkedList"
       queue.size="100000" queue.filename="fwdqueue" queue.saveonshutdown="on")
```
- Очередь для сетевого форвардинга (устойчивость к сбоям)

```
ruleset(name="R1") { action(type="omfile" file="/var/log/r1.log") }
input(type="imudp" port="5514" ruleset="R1")
```
- Разные правила для разных входов (rulesets)


## 🟤 Уровень 7 — journald, ротация, отладка

```
module(load="imjournal" StateFile="/var/lib/rsyslog/imjournal.state")
```
- Читать логи из journald (с сохранением позиции)

Ротация логов обычно настраивается через **logrotate**:  
`/etc/logrotate.d/rsyslog` (пример для Debian/Ubuntu) — управляет файлами в `/var/log/`.

Отладка/проверка конфигов:
```
rsyslogd -N1        # Проверка синтаксиса
rsyslogd -dn        # Фореграунд + debug
systemctl restart rsyslog && journalctl -u rsyslog -e
```


## 🧩 Бонус — практические сценарии

**Разнести nginx‑доступ/ошибки по отдельным файлам:**
```
if ($programname startswith "nginx") then {
  if ($msg contains "error") then action(type="omfile" file="/var/log/nginx/error-rsyslog.log")
  else action(type="omfile" file="/var/log/nginx/access-rsyslog.log")
}
```

**Писать JSON‑строки в один файл и форвардить копию в SIEM:**
```
template(name="JsonOnly" type="list") {
  property(name="msg")
  constant(value="\n")
}
if ($msg contains "{") then {
  action(type="omfile" file="/var/log/json.log" template="JsonOnly")
  action(type="omfwd" target="siem.lan" port="6514" protocol="tcp")
}
```

**Выключить rate‑limit для пиковых UDP‑логов:**
```
module(load="imudp" RateLimit.Interval="0")
```

**Разделить логи по контейнерам (по hostname/programname):**
```
template(name="ByCtr" type="string" string="/var/log/ctr/%hostname%/%programname%.log")
action(type="omfile" dynaFile="ByCtr")
```

**Фильтр по UID/процессу:**
```
if ($syslogtag contains "sudo") then action(type="omfile" file="/var/log/sudo.log")
```

---

### Полезные напоминалки
- После правки конфигов: `sudo systemctl restart rsyslog`
- Проверка синтаксиса без рестарта: `sudo rsyslogd -N1`
- Тестовые сообщения: `logger -p local0.info "deploy ok"` и смотрим `local0.*` правила
- Для TLS убедись, что порты 6514/TCP открыты и сертификаты корректны

