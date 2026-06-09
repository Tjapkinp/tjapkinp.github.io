
`jq` — CLI‑процессор JSON: фильтруй, преобразуй, агрегируй и форматируй JSON‑данные прямо в терминале; идеально сочетается с `curl`.

## 📝 Топ‑20 самых полезных флагов `jq`
- `-r, --raw-output` — печатать строки **без** кавычек (сырой вывод).
- `-c, --compact-output` — компактный JSON (в одну строку).
- `-M, --monochrome-output` — без цветной подсветки.
- `-S, --sort-keys` — сортировать ключи объектов по алфавиту.
- `-e` — выход с кодом 0/1 в зависимости от наличия результата (для скриптов).
- `-s, --slurp` — проглотить весь ввод как **массив** JSON‑документов.
- `-n, --null-input` — не читать stdin; запускать фильтр без входа (генерация).
- `-f FILE` — загрузить фильтр из файла.
- `-R, --raw-input` — читать сырой текст и интерпретировать как строки.
- `--arg NAME VAL` — передать строковый аргумент в фильтр (`$NAME`).
- `--argjson NAME JSON` — передать JSON‑значение в фильтр.
- `--slurpfile NAME FILE` — загрузить файл как массив JSON‑значений (`$NAME`).
- `--rawfile NAME FILE` — загрузить файл как **строку** (`$NAME`).
- `-L PATH` — добавить каталог библиотек `jq` (для `import`).
- `--indent N` — задать шаг отступа в пробелах (форматирование).
- `--tab` — отступы табами.
- `-C, --color-output` — принудительно включить цветной вывод.
- `--stream` — потоковый режим: события парсинга (для **очень больших** JSON).
- `-j, --join-output` — склеить результаты без разделителей.
- `--argfile NAME FILE` — (в новых сборках) передать JSON из файла как `$NAME`.

---

# 🚀 Айсберг команд `jq` — от базового к гуру

## 🟢 Уровень 1 — База (разогрев)

`echo '{"name":"Ada","age":25}' | jq '.'`
- Красиво отформатировать JSON

`cat data.json | jq '.items'`
- Достать поле/массив `items`

`cat data.json | jq '.items[0]'`
- Первый элемент массива

`cat data.json | jq '.user.name'`
- Вложенные поля через точку

`cat data.json | jq -r '.user.email'`
- Сырой вывод строки (без кавычек)


## 🟡 Уровень 2 — Фильтрация и условия

`cat data.json | jq '.users[] | select(.active == true)'`
- Оставить только активных пользователей

`cat data.json | jq '.users[] | select(.age >= 18) | .name'`
- Имена пользователей 18+

`cat data.json | jq '.items[] | select(.tags | index("linux"))'`
- Элементы, содержащие тег `linux`

`cat data.json | jq '.items[] | select(.price? // 0 > 100)'`
- Фильтр по цене, устойчивый к отсутствию поля

`cat data.json | jq 'map(select(.ok))'`
- Отфильтровать массив по условию (весь массив на входе)


## 🟠 Уровень 3 — Преобразования и маппинг

`cat data.json | jq '.users | map({name, email})'`
- Сузить объекты к нужным полям

`cat data.json | jq '.users | map({key: .id, value: .name}) | from_entries'`
- Превратить массив в объект по ключу

`cat data.json | jq '.items | map(.price) | add'`
- Сумма цен

`cat data.json | jq '[.users[].age] | add / length'`
- Средний возраст

`cat data.json | jq '.users | group_by(.role) | map({role: .[0].role, count: length})'`
- Группировка по роли с подсчётом


## 🔵 Уровень 4 — Регулярки, строки и даты

`cat data.json | jq -r '.users[] | .email | select(test("@example\\.com$"))'`
- E‑mail домена `example.com` (регэксп через `test`)

`cat data.json | jq -r '.users[] | "\(.id),\(.name),\(.age)"'`
- Форматирование строками (интерполяция)

`date +%s | jq -R 'tonumber | todate'`
- Преобразовать UNIX‑timestamp в дату

`echo '"2025-09-01T12:00:00Z"' | jq 'fromdateiso8601 | todateiso8601'`
- Туда‑обратно ISO‑дата


## 🟣 Уровень 5 — Работа с файлами и доп. входом

`jq --arg token "$TOKEN" '.auth = $token' config.json`
- Вставить значение переменной окружения в JSON

`jq --argjson limits '{"cpu":2,"mem":"2g"}' '.limits = $limits' config.json`
- Вставить объект из JSON‑аргумента

`jq --slurpfile extra extra.json '.data += $extra' base.json`
- Подмешать массив из другого файла

`jq --rawfile note note.txt '.note = $note' data.json`
- Вставить сырой текст файла как строку


## 🔴 Уровень 6 — Потоки, большие данные, производительность

`jq -c '.items[]' big.json`
- Выводить элементы массивов **построчно** (компактно)

`jq -s 'add' parts/*.json`
- Склеить несколько JSON‑файлов в один (массив → суммировать/объединить)

`jq --stream 'select(length==2 and (.[0][-1]=="id")) | .[1]' huge.json`
- Потоковый разбор: доставать значения по ключу `id` без загрузки всего файла в память

`jq -n --arg v "$(date +%F)" '{date:$v, ok:true}'`
- Генерация JSON «с нуля» без stdin


## 🟤 Уровень 7 — Модули, функции и продвинутое

`jq -n -f script.jq data.json`
- Запустить фильтры из файла

`cat data.json | jq 'def upper(s): s | ascii_upcase; .name |= upper(.)'`
- Объявить функцию и применить её

`cat data.json | jq 'reduce .items[] as $i (0; . + $i.price)'`
- `reduce`: аккумулировать значения

`cat data.json | jq 'walk(if type=="object" then with_entries(.key |= ascii_downcase) else . end)'`
- `walk`: рекурсивно обойти структуру (требует jq ≥1.6)


## 🧩 Бонус — практические рецепты

**Отформатировать и отсортировать ключи:**
```
cat data.json | jq -S .
```

**Вытащить все уникальные значения поля:**
```
jq -r '.[].category' data.json | sort -u
```

**CSV из JSON массива объектов:**
```
jq -r '(.[0] | keys_unsorted) as $h | $h, map([.[ $h[] ]])[] | @csv' data.json
```

**Сравнить два JSON (логические различия по полям):**
```
jq -n --argfile a a.json --argfile b b.json '$a == $b'
```

**Фильтр curl → jq (читаемый API‑ответ):**
```
curl -s https://api.github.com/repos/stedolan/jq | jq '{name, stargazers_count, license: .license.spdx_id}'
```
