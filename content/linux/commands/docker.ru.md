
# 🐳 Docker: кратко и по делу
Docker — изолированная упаковка и запуск приложений в контейнерах: собирай образы, запускай контейнеры, масштабируй и деплой без «а у меня работает».

## 📝 Топ‑20 самых полезных флагов Docker (CLI)
- `-d` — запуск контейнера в фоне (detached).
- `-p HOST:CONT` — проброс порта хост→контейнер (`docker run -p 8080:80`).
- `-v SRC:DEST[:MODE]` — монтирование тома/директории (`:ro`, `:z` для SELinux).
- `-e KEY=VAL` — задать переменную окружения.
- `--env-file FILE` — загрузить переменные окружения из файла.
- `--name NAME` — имя контейнера.
- `--rm` — удалять контейнер по завершении.
- `-it` — интерактивный TTY (совместно `-i` и `-t`).
- `--network NET` — подключить к сети (bridge/host/none/или пользовательская).
- `--restart=POLICY` — политика рестартов (`no`, `on-failure`, `always`, `unless-stopped`).
- `--cpus N` — ограничить CPU.
- `-m/--memory SIZE` — ограничить RAM (напр. `512m`, `2g`).
- `--gpus all|"device=…"` — проброс GPU (NVIDIA, при установленном runtime).
- `-u/--user UID[:GID]` — запуск от имени пользователя в контейнере.
- `-w/--workdir PATH` — рабочая директория процесса.
- `--entrypoint CMD` — переопределить ENTRYPOINT образа.
- `--add-host host:ip` — дописать запись в `/etc/hosts` контейнера.
- `--log-driver DRIVER` — драйвер логов (json-file, local, journald и т.д.).
- `--health-cmd "CMD"` — healthcheck-команда (с `--health-interval`, `--retries`).
- `--build-arg KEY=VAL` — аргумент сборки для `docker build` (используется в Dockerfile как `ARG`).

---

# 🚀 Айсберг команд `docker` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`docker version`
- Версия клиента/демона

`docker info`
- Сводка: драйверы, лимиты, плагины, версии

`docker images`
- Список локальных образов

`docker ps`
- Запущенные контейнеры

`docker ps -a`
- Все контейнеры (включая остановленные)

`docker pull nginx:latest`
- Скачать образ

`docker run hello-world`
- Проверить, что Docker работает

`docker run -it --rm ubuntu bash`
- Одноразовая интерактивная сессия в Ubuntu

`docker stop CONTAINER && docker rm CONTAINER`
- Остановить и удалить контейнер

`docker rmi IMAGE`
- Удалить образ


## 🟡 Уровень 2 — Сборка и образы

`docker build -t myapp:dev .`
- Собрать образ из Dockerfile в текущей папке

`docker tag myapp:dev myrepo/myapp:dev`
- Проставить новый тег/репозиторий

`docker push myrepo/myapp:dev`
- Запушить образ в реестр (Docker Hub/регистри)

`docker history IMAGE`
- История слоёв образа

`docker inspect IMAGE_or_CONTAINER`
- Подробный JSON об образе/контейнере

`.dockerignore`
- Исключить файлы из контекста сборки (ускорение/безопасность)

`docker save IMAGE | gzip > image.tar.gz`
- Экспорт образа в tar-архив

`docker load < image.tar.gz`
- Импорт образа из архива


## 🟠 Уровень 3 — Порты, тома, копирование

`docker run -d --name web -p 8080:80 nginx`
- Запустить Nginx и пробросить 8080→80

`docker run -d --name app -v "$PWD":/app node:18`
- Bind‑mount текущей директории в контейнер

`docker volume create data && docker run -d -v data:/var/lib/postgresql/data postgres`
- Именованный том для данных БД

`docker cp CONTAINER:/path/in/ctr ./local_dir`
- Скопировать файлы из контейнера на хост

`docker cp ./local_file CONTAINER:/path/in/ctr`
- Скопировать файлы с хоста в контейнер

`docker exec -it web sh`
- Зайти в работающий контейнер (alpine/sh или bash)


## 🔵 Уровень 4 — Логи, ресурсы, перезапуски

`docker logs -f --tail=100 web`
- Последние 100 строк и «следить» за логами

`docker top web`
- Процессы внутри контейнера

`docker stats`
- Онлайн‑метрики CPU/RAM/IO/NET

`docker update --cpus 1.5 -m 512m web`
- Ограничить ресурсы уже работающему контейнеру

`docker restart web`
- Перезапустить контейнер

`docker run --restart=unless-stopped …`
- Автоматический рестарт контейнера


## 🟣 Уровень 5 — Сети и DNS

`docker network ls`
- Список сетей (bridge/host/none/пользовательские)

`docker network create --driver bridge devnet`
- Пользовательская bridge‑сеть

`docker run -d --network devnet --name redis redis`
- Запустить контейнер в своей сети

`docker network inspect devnet`
- Посмотреть IP/alias/подключения

`docker network connect devnet web`
- Подключить существующий контейнер к сети

`docker run --net=host …`
- Сетевой стек хоста (осторожно с портами/безопасностью)


## 🔴 Уровень 6 — Оптимальная сборка (BuildKit, кросс‑платформа)

`export DOCKER_BUILDKIT=1`
- Включить BuildKit (быстрее, кэш/секреты/параллелизм)

`docker build --no-cache -t myapp:clean .`
- Сборка без кэша

`docker build --pull -t myapp:latest .`
- Всегда подтягивать свежие базовые слои

`docker build --build-arg NODE_ENV=production -t myapp:prod .`
- Передать аргументы в Dockerfile (`ARG NODE_ENV`)

`docker buildx create --use && docker buildx build --platform linux/amd64,linux/arm64 -t myrepo/app:multi --push .`
- Мультиарх‑сборка и публикация одним шагом

`# Dockerfile (фрагмент): multi‑stage`
- `FROM golang:1.22 AS build`
- `RUN go build -o /out/app`
- `FROM gcr.io/distroless/base`
- `COPY --from=build /out/app /app`
- `ENTRYPOINT ["/app"]`


## 🟤 Уровень 7 — Безопасность, изоляция и продвинутое

`docker run --read-only --tmpfs /tmp -u 1000:1000 --cap-drop ALL --security-opt no-new-privileges …`
- Read‑only rootfs, tmpfs, без привилегий и от небезопасного root

`docker run --health-cmd "curl -fsS http://localhost:8080/health || exit 1" --health-interval=10s --health-retries=3 …`
- Healthcheck для контейнера

`docker run --add-host internal.api:10.0.0.5 …`
- Доп. запись в `/etc/hosts`

`docker system df`
- Использование диска образами/контейнерами/томами

`docker system prune -af --volumes`
- Агрессивная очистка всего неиспользуемого (осторожно!)

`docker export CONTAINER | gzip > rootfs.tar.gz`
- Экспорт файловой системы контейнера

`docker import rootfs.tar.gz myimg:raw`
- Импорт в образ из tar

`docker events`
- Поток событий демона (отладка/мониторинг)


## 🧩 Docker Compose (мини‑раздел)

`docker compose up -d`
- Поднять все сервисы в фоне (из `docker-compose.yml`)

`docker compose down --volumes --remove-orphans`
- Остановить, удалить контейнеры/сети/осиротевшие, удалить тома

`docker compose logs -f app`
- Следить за логами сервиса `app`

`docker compose exec app sh`
- Выполнить команду внутри сервиса

`docker compose build --no-cache`
- Пересобрать сервисы без кэша

`docker compose pull && docker compose up -d`
- Обновить образы и перезапустить сервисы

```yaml
# Пример docker-compose.yml (фрагмент)
version: "3.9"
services:
  web:
    image: nginx:alpine
    ports: ["8080:80"]
    depends_on: [app]
  app:
    build: .
    env_file: .env
    restart: unless-stopped
    volumes: ["./data:/data"]
    healthcheck:
      test: ["CMD-SHELL","curl -fsS http://localhost:8080/health || exit 1"]
      interval: 10s
      retries: 3
networks: { default: { name: devnet } }
```

