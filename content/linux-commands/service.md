
`service` — фронтенд для управления **SysV init**-скриптами (`/etc/init.d/<name>`): запуск, остановка, перезапуск и статус демонов.  
В системах с **systemd** часто перенаправляется на `systemctl` (совместимость), но поведение может отличаться.

## 📝 Топ‑20 полезных команд и опций `service`
- `service --status-all` — показать статус всех сервисов (знаки `+/-/?`).
- `service <name> status` — статус конкретного сервиса.
- `service <name> start` — запустить сервис.
- `service <name> stop` — остановить сервис.
- `service <name> restart` — перезапустить сервис.
- `service <name> reload` — перечитать конфиг без прерывания работы (если поддерживается).
- `service <name> force-reload` — форсированная перезагрузка конфигов.
- `service <name> try-restart` — перезапуск только если сервис уже запущен.
- `service <name> condrestart` — аналог try‑restart (в некоторых дистрибутивах).
- `service --full-restart <name>` — полная перезагрузка (есть не везде).
- `service --help` — справка по команде.
- `service --version` — версия (если реализовано в дистрибутиве).
- `invoke-rc.d <name> <action>` — «правильный» вызов скриптов init в Debian‑семействе (уважает политики).
- `update-rc.d <name> defaults` — включить автозапуск SysV‑скрипта (Debian‑семейство).
- `update-rc.d <name> disable|enable` — управление автозапуском (Debian).
- `chkconfig <name> on|off` — управление автозапуском (RHEL/CentOS SysV).
- `rcconf` / `sysv-rc-conf` — TUI‑утилиты для автозапуска (если установлены).
- `service --status-all | grep '+'` — показать только сервисы «запущен».
- `runlevel` — текущий runlevel (SysV‑системы).
- `who -r` — информация о текущем runlevel/состоянии.

> Примечание: В systemd‑системах предпочтительно использовать `systemctl start|stop|status ...`. `service` останется полезным в старых или совместимых окружениях, а также в контейнерах/минималках.

---

# 🚀 Айсберг команд `service` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`service --status-all`
- Список всех сервисов и их статусы (`+` running, `-` stopped, `?` неизвестно)

`service nginx status`
- Проверить статус Nginx

`service nginx start`
- Запустить Nginx

`service nginx stop`
- Остановить Nginx

`service nginx restart`
- Перезапустить Nginx


## 🟡 Уровень 2 — Перезагрузка конфигов и мягкие действия

`service nginx reload`
- Перечитать конфиг без простоя (graceful reload)

`service rsyslog force-reload`
- Форсированная перечитка конфигов

`service ssh try-restart`
- Перезапуск только если SSH уже запущен

`service cron status || service cron start`
- Запустить, если не работает

`service mysql reload || service mysql restart`
- Мягкая перечитка или fallback‑рестарт


## 🟠 Уровень 3 — Автозапуск (SysV инструменты)

`sudo update-rc.d nginx defaults`
- Включить автозапуск для Debian‑семейства

`sudo update-rc.d nginx disable`
- Отключить автозапуск

`sudo chkconfig nginx on`
- Включить автозапуск (RHEL/CentOS SysV)

`sudo chkconfig --list`
- Посмотреть уровни запуска

`invoke-rc.d nginx restart`
- «Корректный» вызов с учётом политик (Debian)


## 🔵 Уровень 4 — Инспекция и отладка скриптов

`ls -l /etc/init.d/`
- Список init‑скриптов

`head -n 50 /etc/init.d/nginx`
- Посмотреть шебанг, LSB‑хедеры и команды

`grep -E "start|stop|reload" -n /etc/init.d/nginx`
- Найти поддерживаемые действия

`sh -x /etc/init.d/nginx start`
- Трассировка запуска скрипта (debug)

`service nginx status; echo $?`
- Код выхода статуса (0 — ок, >0 — проблема)


## 🟣 Уровень 5 — Runlevel и порядок запуска

`runlevel`
- Текущий runlevel (например, 3/5)

`ls /etc/rc*.d/ | grep nginx`
- Узнать порядок ссылок S/K для сервиса

`update-rc.d nginx defaults 20`
- Установить приоритет запуска (20)

`update-rc.d -f nginx remove`
- Удалить все ссылки runlevel для сервиса

`who -r`
- Информация о состоянии и уровне запуска


## 🔴 Уровень 6 — Контейнеры и смешанные окружения

`service nginx status || systemctl status nginx`
- Портируемые one‑liner’ы для любых систем

`service ssh restart || rc-service ssh restart`
- Fallback для OpenRC (Alpine)

`docker exec -it <ctr> service nginx start`
- Управление сервисом внутри контейнера

`ansible all -a "service nginx restart" -b`
- Массовое управление сервисами через Ansible

`service --status-all | grep -E 'nginx|apache2'`
- Быстро сравнить статусы веб‑сервисов


## 🟤 Уровень 7 — Гуру‑режим

`for s in nginx php-fpm mysql; do service $s status || service $s start; done`
- Гарантировать, что ключевые сервисы запущены

`service --status-all | awk '/\+/{print $2}' | xargs -r -n1 service`
- Пробежать `service <name>` для всех запущенных

`timeout 5 service nginx status || echo "nginx: timeout"`
- Отладка зависаний статуса

`( service nginx reload && echo OK ) || ( echo FAIL; service nginx restart )`
- Безотказная перечитка с fallback‑рестартом

`grep -R "Provides:" /etc/init.d | column -t`
- Инвентаризация SysV‑скриптов по LSB‑меткам


## 🧩 Бонус — практические сценарии

**Старт/стоп сервисов по списку:**
```
for s in ssh rsyslog cron; do service $s restart; done
```

**Проверить все упавшие сервисы:**
```
service --status-all | grep '-'
```

**Бережный деплой Nginx:**
```
service nginx reload || service nginx restart
```

**Автозапуск (Debian):**
```
update-rc.d myapp defaults
```

**Работа в смешанной среде (SysV + systemd):**
```
service myapp restart || systemctl restart myapp
```
