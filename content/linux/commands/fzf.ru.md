
# 🔍 `fzf`: кратко и по делу
`fzf` — интерактивный инструмент для **поиска и фильтрации** данных в реальном времени.  
Ищет файлы, строки, процессы, команды, интегрируется с `git`, `ranger`, `ripgrep`, `fd`, `tmux` и автодополнением shell.

---

## 📝 Топ‑20 ключевых флагов и приёмов `fzf`
- `fzf` — поиск по списку файлов (stdin или текущая директория).
- `fzf < file.txt` — искать в переданном списке.
- `find . | fzf` — интерактивный поиск файлов.
- `fzf --preview 'cat {}'` — превью содержимого файлов.
- `fzf --height 40%` — уменьшить окно поиска.
- `fzf --reverse` — панель поиска внизу.
- `fzf --multi` — множественный выбор.
- `fzf --bind "tab:toggle+down"` — переключение выбора клавишей Tab.
- `fzf --bind "ctrl-a:select-all"` — выделить всё.
- `fzf --bind "ctrl-d:deselect-all"` — снять выделение.
- `fzf --preview 'bat --style=numbers --color=always {}'` — красивое превью кода.
- `fzf --ansi` — поддержка цветного вывода.
- `fzf --query "keyword"` — сразу искать по запросу.
- `fzf --exact` — поиск по точному совпадению.
- `fzf --tiebreak=end` — сортировать совпадения по порядку.
- `fzf --cycle` — циклическая навигация по результатам.
- `fzf --layout=reverse-list` — вывод совпадений сверху вниз.
- `fzf --delimiter=: --nth=2` — искать только по второму полю.
- `fzf --border` — добавить рамку.
- `fzf --help` — помощь.

---

# ⌨️ Горячие клавиши `fzf`
- `Tab` — выделить / снять выделение.
- `Shift+Tab` — инвертировать выбор.
- `Enter` — выбрать результат.
- `Ctrl-a` — выбрать всё.
- `Ctrl-d` — снять всё.
- `Ctrl-r` — поиск по истории команд.
- `Ctrl-t` — вставить путь к выбранному файлу в команду.
- `Ctrl-o` — открыть результат в редакторе.
- `Ctrl-y` — скопировать результат в буфер обмена.
- `Esc` — выйти.

---

# 🚀 Айсберг команд `fzf` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`fzf`
- Поиск файлов в текущей директории

`find . | fzf`
- Поиск по результатам `find`

`cat list.txt | fzf`
- Поиск по списку из файла

`history | fzf`
- Поиск по истории команд

`ps aux | fzf`
- Поиск процессов


## 🟡 Уровень 2 — Множественный выбор и превью

`fzf --multi`
- Выбрать несколько файлов

`fzf --preview 'cat {}'`
- Показывать содержимое выбранного файла

`fzf --preview 'head -n 20 {}'`
- Показывать первые 20 строк

`fzf --preview 'ls -lh {}'`
- Предпросмотр метаданных

`fzf --ansi`
- Поддержка цветного вывода


## 🟠 Уровень 3 — Интеграция с ripgrep и grep

`rg --files | fzf`
- Быстрый поиск файлов с `ripgrep`

`rg --line-number "error" | fzf --delimiter : --nth 3..`
- Найти ошибки в коде

`grep -R "TODO" * | fzf`
- Искать TODO в проекте

`grep -R "main()" src | fzf --preview 'bat --style=plain {}'`
- Подсветка совпадений в превью

`fzf --query "main"`
- Сразу открыть поиск по слову "main"


## 🔵 Уровень 4 — Интеграция с git

`git ls-files | fzf`
- Быстрый поиск файлов в репозитории

`git log --oneline | fzf`
- Поиск по истории коммитов

`git branch | fzf`
- Выбор ветки

`git stash list | fzf`
- Поиск по stash

`git diff --name-only | fzf --multi | xargs git add`
- Добавить выбранные файлы в коммит


## 🟣 Уровень 5 — Поиск по истории и автодополнение

`CTRL+r`
- История команд bash/zsh

`export FZF_DEFAULT_COMMAND='fd --type f'`
- Использовать `fd` вместо `find`

`export FZF_DEFAULT_OPTS="--height 40% --reverse --border"`
- Глобальные настройки

`bind '"\C-t": "$(fzf)"\n'`
- Автодополнение файлов

`export FZF_CTRL_T_COMMAND="rg --files"`
- Более быстрый поиск


## 🔴 Уровень 6 — Гуру‑режим и макросы

`fzf --bind "ctrl-a:select-all,ctrl-d:deselect-all"`
- Полный контроль выбора

`fzf --bind "change:reload(fd .)"`
- Динамическая перезагрузка списка

`fzf --delimiter=: --nth=2 --preview 'awk -F: "{print $2}" {}'`
- Поиск по полям CSV

`find . -type f | fzf --multi --preview 'head -n 10 {}'`
- Быстрое превью нескольких файлов

`fzf --exact --tiebreak=end`
- Строгий поиск


## 🟤 Уровень 7 — Автоматизация и сценарии

`fzf --multi --preview 'bat --style=plain --color=always {}' | xargs -o vim`
- Открыть выбранные файлы в Vim

`journalctl -xe | fzf`
- Быстрый просмотр логов

`ssh $(cat ~/.ssh/known_hosts | cut -d, -f1 | fzf)`
- Подключение к серверу через fzf

`history | fzf | xargs -I {} echo "Выполню команду: {}"`
- Подбор и запуск команды

`find . | fzf --multi | xargs rm -i`
- Удаление выбранных файлов


## 🧩 Бонус — практические сценарии

**Выбор файла в текущей директории:**
```
fzf
```

**Поиск коммитов в git:**
```
git log --oneline | fzf
```

**Интеграция с ranger:**
```
:map <C-f> fzf_select
```

**Множественный выбор файлов и передача в Vim:**
```
fzf --multi | xargs vim
```

**Поиск процессов и завершение выбранного:**
```
ps aux | fzf | awk '{print $2}' | xargs kill -9
```
