
`systemctl` — основной инструмент для управления сервисами, монтированием, таймерами и юнитами в системе с **systemd**.  
Позволяет запускать, останавливать, перезапускать сервисы, управлять загрузкой, проверять статусы и анализировать зависимости.

## 📝 Топ‑20 полезных флагов `systemctl`
- `start <unit>` — запустить сервис.
- `stop <unit>` — остановить сервис.
- `restart <unit>` — перезапустить сервис.
- `reload <unit>` — перезагрузить конфигурацию без остановки.
- `enable <unit>` — включить автозапуск при старте.
- `disable <unit>` — отключить автозапуск.
- `status <unit>` — показать состояние сервиса.
- `is-active <unit>` — проверка, запущен ли сервис.
- `is-enabled <unit>` — проверка, включён ли автозапуск.
- `list-units` — список активных юнитов.
- `list-unit-files` — список всех юнитов и их состояния.
- `show <unit>` — вывести параметры юнита.
- `cat <unit>` — показать файл юнита.
- `edit <unit>` — редактировать юнит через override.
- `daemon-reload` — перезагрузить systemd после изменений.
- `list-timers` — список активных таймеров.
- `mask <unit>` — полностью заблокировать запуск юнита.
- `unmask <unit>` — разблокировать юнит.
- `list-dependencies <unit>` — показать зависимости юнита.
- `rescue` / `emergency` — перевести систему в аварийный режим.

---

# 🚀 Айсберг команд `systemctl` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`systemctl status`
- Показать общую информацию о системе

`systemctl status ssh`
- Проверить статус SSH-сервиса

`systemctl start ssh`
- Запустить SSH

`systemctl stop ssh`
- Остановить SSH

`systemctl restart ssh`
- Перезапустить SSH


## 🟡 Уровень 2 — Управление автозапуском

`systemctl enable nginx`
- Включить автозапуск Nginx

`systemctl disable nginx`
- Отключить автозапуск

`systemctl is-enabled nginx`
- Проверить, включен ли автозапуск

`systemctl enable --now docker`
- Включить автозапуск и сразу запустить

`systemctl disable --now docker`
- Выключить автозапуск и остановить сервис


## 🟠 Уровень 3 — Работа с логами и статусами

`systemctl status nginx`
- Статус и последние логи Nginx

`systemctl show ssh`
- Все параметры юнита SSH

`systemctl cat docker`
- Показать конфигурацию юнита Docker

`systemctl list-units --type=service`
- Список активных сервисов

`systemctl list-unit-files --type=service`
- Список всех сервисов и их состояния


## 🔵 Уровень 4 — Таймеры и планировщики

`systemctl list-timers`
- Список активных таймеров

`systemctl status apt-daily.timer`
- Проверить конкретный таймер

`systemctl stop apt-daily.timer`
- Остановить таймер

`systemctl disable apt-daily.timer`
- Отключить таймер

`systemctl start my-backup.timer`
- Запустить кастомный таймер


## 🟣 Уровень 5 — Диагностика и зависимость сервисов

`systemctl list-dependencies nginx`
- Показать зависимости Nginx

`systemctl list-dependencies --reverse nginx`
- Показать, кто зависит от Nginx

`systemctl show nginx | grep ExecStart`
- Узнать, какую команду запускает юнит

`systemctl is-active docker`
- Проверить, запущен ли Docker

`systemctl is-failed ssh`
- Проверить, упал ли сервис


## 🔴 Уровень 6 — Редактирование юнитов и advanced override

`sudo systemctl edit nginx`
- Создать override-файл для Nginx

`sudo systemctl edit --full nginx`
- Полностью редактировать юнит

`sudo systemctl daemon-reload`
- Перечитать конфиги systemd

`sudo systemctl restart nginx`
- Применить изменения

`systemctl revert nginx`
- Удалить кастомные override-настройки


## 🟤 Уровень 7 — Гуру‑режим

`systemctl mask NetworkManager`
- Полностью заблокировать запуск NetworkManager

`systemctl unmask NetworkManager`
- Разблокировать его

`systemctl rescue`
- Перейти в rescue mode

`systemctl emergency`
- Перейти в emergency mode

`journalctl -u nginx -f`
- Смотреть логи сервиса в реальном времени


## 🧩 Бонус — практические сценарии

**Перезапустить все упавшие сервисы:**
```
systemctl list-units --state=failed
sudo systemctl restart <unit>
```

**Перечитать конфиги systemd после обновлений:**
```
sudo systemctl daemon-reload
```

**Включить автоматический рестарт сервиса:**
```
sudo systemctl edit nginx
[Service]
Restart=always
```

**Смотреть активные сервисы в реальном времени:**
```
watch "systemctl list-units --type=service"
```

**Отфильтровать только неактивные сервисы:**
```
systemctl list-units --state=inactive
```
