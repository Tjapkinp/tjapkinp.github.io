
# 🔧 `tee`: кратко и по делу
`tee` — читает **stdin** и одновременно пишет в **stdout** и один/несколько файлов.  
Идеален для логирования, отладки пайпов, записи под `sudo`, разветвления вывода на разные команды (через process substitution).

## 📝 Топ‑20 полезных флагов и приёмов `tee` (GNU coreutils)
- `-a, --append` — **добавлять** в конец файла, не перезаписывать.
- `-i, --ignore-interrupts` — игнорировать SIGINT (Ctrl+C) во время записи.
- `--output-error=MODE` — реакция на ошибки записи: `warn`, `warn-nopipe`, `exit`, `exit-nopipe`.
- Запись в **несколько файлов**: `tee file1 file2 file3`.
- Совместно с `sudo` для записи в root‑файлы: `... | sudo tee /root/file`.
- Глушить вывод в терминал: `... | tee file >/dev/null`.
- Разветвлять поток на процессы: `tee >(cmd1) >(cmd2)` (bash/zsh).
- Вести **живой лог**: `cmd | tee -a app.log`.
- Сохранять и одновременно парсить: `cmd | tee out.txt | awk ...`.
- Писать в **FIFO/pipe** и файлы одновременно.
- Поддерживает `--help`, `--version`.
- POSIX‑совместимая базовая форма: `tee [-ai] file...`.
- Работает с бинарными данными (без трансформаций).
- Можно использовать для **тестов производительности** с `/dev/null`.
- Хорош для **отладки CI**: сохранять лог, продолжая стримить в консоль.
- Устойчивые пайпы: в связке с `set -o pipefail` фиксирует ошибки записи.
- С `stdbuf -oL` для построчного флашинга выводов в реальном времени.
- Перенаправление stderr в stdin `2>&1 | tee file` для захвата ошибок.
- С `script -f`/`unbuffer` для программ, которые буферизуют вывод.
- Комбинировать с `logger` для зеркалирования в syslog.

---

# 🚀 Айсберг команд `tee` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`echo "hello" | tee hello.txt`
- Записать в файл и показать на экране

`printf "line\n" | tee -a log.txt`
- **Добавить** строку в файл (append)

`cat input.txt | tee copy.txt > /dev/null`
- Сохранить копию, не выводя в терминал

`date | tee -a timeline.txt`
- Вести простой лог со штампами времени

`ls | tee files.txt | wc -l`
- Сохранить список и параллельно посчитать строки


## 🟡 Уровень 2 — С `sudo`, stderr и «тихим» терминалом

`echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward`
- Записать в root‑файл (обход ограничений `>` с `sudo`)

`somecmd 2>&1 | tee run.log`
- Захватить и stdout, и stderr в один лог

`cmd | tee out.log >/dev/null`
- Писать в файл, но **не** показывать на экране

`cmd | sudo tee -a /var/log/custom.log`
- Аппенд в системный лог с правами root

`make 2>&1 | tee build.log | grep -i error`
- Лог + онлайн‑фильтрация ошибок


## 🟠 Уровень 3 — Разветвление потока (process substitution)

`cmd | tee >(grep ERROR > errors.txt) >(awk '{print NF}' > fields.txt) > /dev/null`
- Развести поток: ошибки в один файл, статистику — в другой

`tail -f app.log | tee >(grep -i warn > warn.log) >(grep -i error > error.log)`
- Онлайн‑демультиплексирование лога

`journalctl -f | tee >(awk '{print $3}' > units.txt)`
- Одним взглядом — и лог, и выборка юнитов


## 🔵 Уровень 4 — Управление ошибками и буферами

`cmd | tee --output-error=exit out.txt`
- Упасть при ошибке записи в файл (например, диск переполнен)

`cmd | tee --output-error=warn out.txt`
- Только предупредить (не прерывать пайп)

`stdbuf -oL cmd | tee -a live.log`
- Построчный вывод в реальном времени

`{ time cmd; } 2>&1 | tee time.log`
- Логирование таймингов команды


## 🟣 Уровень 5 — CI/CD и воспроизводимость

`./run-tests.sh | tee -a ci.log`
- Сохранять полный вывод тестов

`docker build . 2>&1 | tee build.log`
- Лог сборки контейнера

`kubectl logs -f deploy/web | tee -a web.log`
- Живой лог в k8s + запись в файл

`ansible-playbook site.yml -vv | tee ansible.log`
- Полный журнал развертывания


## 🔴 Уровень 6 — Производительность и бинарные потоки

`dd if=/dev/zero bs=1M count=100 | tee /dev/null | wc -c`
- Прогнать 100 МБ через `tee` и измерить размер

`cat image.iso | tee copy.iso | md5sum`
- Параллельно копировать и считать хэш

`gzip -dc data.gz | tee >(md5sum > data.md5) | sqlite3 db.sqlite`
- Хэшировать поток и одновременно загружать в БД


## 🟤 Уровень 7 — Гуру‑режим: сложные сборки потоков

`{ echo "[HEADER]"; cat body.txt; } | tee -a report.txt | sed '1d'`
- Добавить заголовок в файл и параллельно отдать поток дальше

`(cmd1; cmd2) 2>&1 | tee -a all.log | grep -i "fail"`
- Объединить вывод нескольких команд, логировать и фильтровать

`while read -r line; do echo "$line"; done < input | tee >(wc -l > lines.txt)`
- Чтение по строкам + параллельная статистика

`rsync -av --progress src/ dst/ | tee -a rsync.log`
- Логирование прогресса копирования


## 🧩 Бонус — практические рецепты

**Тихо записать вывод команды в файл:**
```
cmd | tee out.txt >/dev/null
```

**Захватить stderr и stdout вместе:**
```
cmd 2>&1 | tee run.log
```

**Под рутом редактировать конфиг через пайп:**
```
printf "option=yes\n" | sudo tee -a /etc/myapp.conf
```

**Реальный «живой» лог:**
```
stdbuf -oL app | tee -a app.log
```

**Развести поток на две обработки:**
```
cmd | tee >(grep -i error > errors.log) >(awk '{print $1}' > firstcol.txt) > /dev/null
```
