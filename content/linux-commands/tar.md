
`tar` — основной инструмент для упаковки, распаковки, сжатия и архивации файлов в Linux. Поддерживает `.tar`, `.tar.gz`, `.tar.bz2`, `.tar.xz`, `.tar.zst` и умеет работать инкрементально.

## 📝 Топ‑20 самых полезных флагов `tar`
- `-c` — создать новый архив.
- `-x` — извлечь файлы из архива.
- `-t` — показать содержимое архива.
- `-f FILE` — указать имя архива (всегда последний перед файлом!).
- `-v` — подробный вывод (verbose).
- `-C DIR` — извлекать или сохранять в конкретную директорию.
- `-z` — сжатие/распаковка через **gzip** (`.tar.gz`).
- `-j` — сжатие/распаковка через **bzip2** (`.tar.bz2`).
- `-J` — сжатие/распаковка через **xz** (`.tar.xz`).
- `--zstd` — сжатие/распаковка через **zstd** (`.tar.zst`).
- `--lzma` — поддержка `.tar.lzma`.
- `--exclude=PATTERN` — исключить файлы по шаблону.
- `--wildcards` — использовать glob‑паттерны при извлечении.
- `--strip-components=N` — игнорировать первые `N` уровней директорий.
- `--same-permissions` — восстановить оригинальные права доступа.
- `--same-owner` — восстановить владельца (root only).
- `--numeric-owner` — игнорировать имена пользователей, использовать UID/GID.
- `--remove-files` — удалить файлы после упаковки.
- `--no-overwrite-dir` — не перезаписывать права директорий при извлечении.
- `--one-file-system` — не уходить за пределы текущей ФС.

---

# 🚀 Айсберг команд `tar` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`tar -cvf archive.tar dir/`
- Создать архив `archive.tar` из директории `dir/`

`tar -xvf archive.tar`
- Распаковать `archive.tar` в текущую директорию

`tar -tvf archive.tar`
- Показать содержимое архива

`tar -cvf docs.tar *.pdf`
- Создать архив из всех PDF‑файлов

`tar -xvf archive.tar -C /tmp`
- Распаковать в `/tmp`


## 🟡 Уровень 2 — Сжатие и распаковка

`tar -czvf archive.tar.gz dir/`
- Создать `.tar.gz` с gzip‑сжатием

`tar -xzvf archive.tar.gz`
- Распаковать `.tar.gz`

`tar -cjvf archive.tar.bz2 dir/`
- Создать `.tar.bz2` с bzip2‑сжатием

`tar -xjvf archive.tar.bz2`
- Распаковать `.tar.bz2`

`tar -cJvf archive.tar.xz dir/`
- Создать `.tar.xz` с xz‑сжатием

`tar -xJvf archive.tar.xz`
- Распаковать `.tar.xz`

`tar --zstd -cvf archive.tar.zst dir/`
- Создать `.tar.zst` с zstd‑сжатием

`tar --zstd -xvf archive.tar.zst`
- Распаковать `.tar.zst`


## 🟠 Уровень 3 — Исключения и выборочные действия

`tar -czvf code.tar.gz --exclude="*.log" src/`
- Архивировать `src/`, исключая все `.log` файлы

`tar -xzvf archive.tar.gz --wildcards "*.conf"`
- Извлечь только `.conf` файлы

`tar --exclude-from=exclude.txt -czvf backup.tar.gz /home`
- Исключить список путей из `exclude.txt`

`tar --remove-files -czvf archive.tar.gz temp/`
- Упаковать и удалить исходные файлы

`tar -czvf archive.tar.gz --exclude-vcs project/`
- Игнорировать `.git`, `.svn` и другие VCS‑директории


## 🔵 Уровень 4 — Работа с правами и владельцами

`tar -xvf archive.tar --same-owner`
- Сохраняет владельцев при распаковке (root only)

`tar -xvf archive.tar --same-permissions`
- Сохраняет права доступа

`tar -xvf archive.tar --numeric-owner`
- Восстанавливает владельцев по UID/GID

`tar -xvf archive.tar --no-overwrite-dir`
- Не изменять права директорий при распаковке

`tar --preserve-permissions -xvf archive.tar`
- Альтернативный способ сохранить все права


## 🟣 Уровень 5 — Инкрементальные архивы и снапшоты

`tar --listed-from=snapshot.file -czvf full_backup.tar.gz /home`
- Создать полный бэкап + сохранить состояние в `snapshot.file`

`tar --listed-from=snapshot.file -czvf incr_backup.tar.gz /home`
- Инкрементальный бэкап на основе предыдущего

`tar -czvf daily_$(date +%F).tar.gz /var/log`
- Автоматизация ежедневных архивов

`tar -C /etc -czvf etc_backup.tar.gz .`
- Архивировать только содержимое `/etc`, без полного пути


## 🔴 Уровень 6 — Огромные архивы и высокая производительность

`tar --use-compress-program=pigz -cvf archive.tar.gz dir/`
- Использовать **pigz** вместо gzip (параллельное сжатие)

`tar --use-compress-program=pbzip2 -cvf archive.tar.bz2 dir/`
- Быстрое bzip2‑сжатие

`tar --use-compress-program=pxz -cvf archive.tar.xz dir/`
- Мгновенное xz‑сжатие с многопоточностью

`tar --zstd -cvf archive.tar.zst --options zstd:compression-level=15 dir/`
- Zstandard с максимальной компрессией

`tar -czf - dir/ | ssh user@host "cat > /tmp/archive.tar.gz"`
- Создать архив и отправить на удалённый сервер по SSH без промежуточного файла

`ssh user@host "tar -czf - /var/www" | tar -xzf - -C ./backup`
- Скопировать файлы с удалённого сервера напрямую в локальный архив


## 🟤 Уровень 7 — Гуру‑режим

`tar --strip-components=1 -xvf archive.tar`
- Игнорировать первый уровень директорий при распаковке

`tar -cf - dir/ | pv | tar -xf - -C /mnt/ssd`
- Отображать прогресс архивации с `pv`

`find /var/log -type f -name "*.log" | tar -czvf logs.tar.gz -T -`
- Упаковать файлы, найденные через `find`

`tar --diff -f archive.tar`
- Проверить различия между архивом и текущей ФС

`tar --checkpoint=. --checkpoint-action=exec='echo "500MB готово"' -czvf huge.tar.gz bigdir/`
- Визуальный прогресс и контроль больших архивов

`tar --tape-length=100M -cvf parts.tar multi/`
- Делит архив на части по 100 МБ


## 🧩 Бонус — частые рецепты

**Создать .tar.gz**
```
tar -czvf backup.tar.gz folder/
```

**Распаковать в другую директорию**
```
tar -xzvf backup.tar.gz -C /opt
```

**Проверить содержимое архива**
```
tar -tvf backup.tar
```

**Упаковать и удалить оригиналы**
```
tar --remove-files -czvf safe.tar.gz folder/
```

**Копировать файлы по SSH**
```
ssh user@host "tar -czf - /var/www" | tar -xzf - -C ./backup
```
