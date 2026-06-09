
# ⏳ `crontab`: кратко и по делу
`crontab` — утилита для управления планировщиком задач **cron** в Linux.  
Позволяет запускать команды, скрипты и программы **по расписанию**: разово, периодически или сложными паттернами.

- Конфиг пользователя: `crontab -e`
- Глобальные настройки: `/etc/crontab`
- Системные задания: `/etc/cron.*` (`hourly`, `daily`, `weekly`, `monthly`)

---

## 📝 Топ‑20 команд и флагов `crontab`
- `crontab -e` — редактировать задачи текущего пользователя.
- `crontab -l` — показать текущие задания.
- `crontab -r` — удалить все задания.
- `crontab -i` — удаление с подтверждением.
- `crontab -u user` — управлять заданиями другого пользователя (root only).
- `crontab -e -u user` — редактировать задания пользователя.
- `crontab -l -u user` — список заданий пользователя.
- `crontab -r -u user` — удалить задания пользователя.
- `crontab -c` — показать путь к cron-директории.
- `crontab -s` — подавлять вывод ошибок.
- `systemctl status cron` / `systemctl status crond` — проверить, работает ли демон.
- `systemctl start cron` — запустить cron.
- `systemctl enable cron` — включить автозапуск.
- `grep CRON /var/log/syslog` *(Debian/Ubuntu)* — посмотреть логи cron.
- `journalctl -u cron` *(systemd)* — логи демона cron.
- `cron.allow` и `cron.deny` — управление правами пользователей.
- `crontab -T` *(BSD)* — проверить корректность синтаксиса.
- `crontab -x` *(Solaris)* — отладка cron.
- `at` — одноразовые задания (см. ниже).
- `anacron` — выполнение пропущенных cron-задач при выключении ПК.

---

# ⏲️ Синтаксис cron
Каждая строка в `crontab` состоит из **6 полей**:
```
*  *  *  *  *  команда
|  |  |  |  |
|  |  |  |  └─ День недели (0-7, где 0 и 7 = воскресенье)
|  |  |  └──── Месяц (1-12)
|  |  └─────── День месяца (1-31)
|  └────────── Час (0-23)
└───────────── Минута (0-59)
```
### Примеры:
- `0 5 * * * /script.sh` → каждый день в 5:00
- `30 8 * * 1 /script.sh` → каждое **понедельник** в 8:30
- `*/15 * * * * /script.sh` → каждые 15 минут
- `0 0 1 * * /script.sh` → первое число месяца в 00:00
- `0 22 * * 1-5 /script.sh` → будни в 22:00
- `@reboot /script.sh` → запускать при старте системы
- `@hourly /script.sh` → раз в час
- `@daily /script.sh` → раз в день
- `@weekly /script.sh` → раз в неделю
- `@monthly /script.sh` → раз в месяц

---

# 🚀 Айсберг команд `crontab` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`crontab -e`
- Редактировать задания пользователя

`crontab -l`
- Показать текущие задания

`crontab -r`
- Удалить все задания

`systemctl status cron`
- Проверить, работает ли cron

`echo "* * * * * date >> ~/cron.log" | crontab -`
- Добавить задание напрямую


## 🟡 Уровень 2 — Простые расписания

`*/5 * * * * /home/user/backup.sh`
- Каждые 5 минут

`0 6 * * * /home/user/script.sh`
- Каждый день в 6 утра

`30 23 * * 5 /home/user/db_backup.sh`
- Каждый **пятницу** в 23:30

`@reboot /home/user/startup.sh`
- Выполнить при старте системы

`@daily /home/user/clean.sh`
- Каждый день в полночь


## 🟠 Уровень 3 — Работа с пользователями

`sudo crontab -e -u www-data`
- Редактировать задачи `www-data`

`sudo crontab -l -u root`
- Просмотр заданий root

`sudo crontab -r -u postgres`
- Удалить cron PostgreSQL

`sudo tail -f /var/log/syslog | grep CRON`
- Следить за выполнением задач

`sudo journalctl -u cron -f`
- Логи демона cron в режиме реального времени


## 🔵 Уровень 4 — Интеграция со скриптами

```
*/10 * * * * /home/user/backup.sh >> /home/user/backup.log 2>&1
```
- Перенаправить stdout и stderr в лог

```
0 0 * * 0 /home/user/cleanup.sh && /home/user/backup.sh
```
- Выполнять несколько команд подряд

```
*/15 * * * * flock -n /tmp/lockfile /home/user/task.sh
```
- Использовать `flock` для защиты от параллельных запусков

```
@reboot sleep 60 && /home/user/startup.sh
```
- Задержка запуска после перезагрузки

```
@weekly test -x /home/user/check.sh && /home/user/check.sh
```
- Выполнять только если файл существует и исполним


## 🟣 Уровень 5 — Переменные окружения

```
MAILTO="admin@example.com"
SHELL=/bin/bash
PATH=/usr/local/bin:/usr/bin:/bin
```
- Отправлять результат выполнения на почту

```
0 3 * * * MAILTO="" /home/user/script.sh
```
- Выполнить без отправки почты

```
0 1 * * * USER=www-data /home/user/script.sh
```
- Выполнить от имени другого пользователя

```
TZ="Europe/Moscow"
0 0 * * * /home/user/local_time_job.sh
```
- Разные задачи с разными часовыми поясами


## 🔴 Уровень 6 — Гуру‑режим

`crontab -e && systemctl restart cron`
- Применить изменения сразу

`echo "*/5 * * * * logger 'CRON triggered'" | sudo tee /etc/cron.d/debug`
- Создать задание через `/etc/cron.d`

`find /etc/cron.* -type f`
- Посмотреть все системные задания

`sudo grep CRON /var/log/syslog`
- Диагностика выполнения

`cat /etc/cron.allow /etc/cron.deny`
- Проверить, кто имеет доступ


## 🟤 Уровень 7 — Альтернативные планировщики

`anacron`
- Запускает пропущенные задачи при старте

`systemd-run --on-calendar="Mon..Fri 02:00" backup.sh`
- Использовать systemd‑таймеры вместо cron

`at 23:00`
- Одноразовые задачи на сегодня

`schtasks /create /sc minute /mo 10 /tn test /tr script.bat`
- Windows-эквивалент


## 🧩 Бонус — практические сценарии

**Бэкап БД каждый день в 3 ночи:**
```
0 3 * * * /usr/local/bin/pg_dump db > /backups/db.sql
```

**Очистка логов каждое воскресенье в полночь:**
```
0 0 * * 0 find /var/log -type f -name "*.log" -delete
```

**Пинг сервера каждые 5 минут и логирование:**
```
*/5 * * * * ping -c 1 192.168.1.1 >> /var/log/ping.log
```

**Автоматический git-pull каждые 30 минут:**
```
*/30 * * * * cd /home/app && git pull
```

**Включение/отключение Wi-Fi по расписанию:**
```
0 7 * * 1-5 nmcli radio wifi on
0 23 * * 1-5 nmcli radio wifi off
```
