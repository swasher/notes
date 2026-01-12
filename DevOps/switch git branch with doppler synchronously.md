Есть такой кейс - сделать ветку в проекте, и с переключением ветки в гите синхронно переключать базу данных.

Настройки базы данных при этом содержатся в doppler.

Кладем такой скрипт в `.git/hooks` (форматирование новых строк обязательно unix-like!!!):

```bash
#!/bin/bash

# 1. Получаем имя текущей ветки
BRANCH=$(git rev-parse --abbrev-ref HEAD)

# 2. Логика соответствия имен
if [ "$BRANCH" == "master" ] || [ "$BRANCH" == "main" ]; then
    # Если ветка master (или main), используем конфиг dev
    DOPPLER_CONFIG="dev"
else
    # Для всех остальных веток используем имя ветки
    # Заменяем символы, которые Doppler может не принять (например, / на _)
    DOPPLER_CONFIG="${BRANCH//\//_}"
fi

echo "Git checkout to '$BRANCH' detected."

# 3. Переключаем Doppler
# Флаг --yes убирает запрос на подтверждение
if doppler setup --config "$DOPPLER_CONFIG" --no-interactive; then
    echo "Successfully switched Doppler to config: $DOPPLER_CONFIG"
else
    echo "Error: Could not switch to Doppler config '$DOPPLER_CONFIG'."
    echo "Make sure the config exists in the Doppler dashboard."
fi

doppler secrets get DATABASE_NAME
```

## Как пользоваться

- Создай ветку в doppler, отденаследовав ее от dev (опция Duplicate config), и нужно назвать `dev_feature_branch`. Ветка будет всегда начинаться, как ее родитель, то есть с `dev_`.
- Создай ветку в git `dev_feature_branch` - обязательно такое же имя.
- Измени настройки в doppler в новой ветке, а именно - имя базы данных (или что желаешь).
- Ветке в гит `master` или `main` соответствует ветка допплер `dev`, это прибито гвоздями в скрипте.
- Теперь можно проверить, как все работает:

*Переключаемся в дополнительную ветку*

```bash
bsh ➜  git switch dev_feature_branch 
Switched to branch 'dev_feature_branch'
Git checkout to 'dev_feature_branch' detected.
Auto-selecting project from repo config file
┌─────────┬────────────────────┬─────────────────────────────────────┐
│ NAME    │ VALUE              │ SCOPE                               │
├─────────┼────────────────────┼─────────────────────────────────────┤
│ config  │ dev_feature_branch │ C:\Users\User\GitHub\MyProject      │
│ project │ print_mes          │ C:\Users\User\GitHub\MyProject      │
└─────────┴────────────────────┴─────────────────────────────────────┘
Successfully switched Doppler to config: dev_feature_branch
┌───────────────┬───────────────────┬──────┐
│ NAME          │ VALUE             │ NOTE │
├───────────────┼───────────────────┼──────┤
│ DATABASE_NAME │ postgres_throught │      │
└───────────────┴───────────────────┴──────┘


bsh ➜  doppler secrets get DATABASE_NAME  --print-config
┌────────────────┬───────────────────────────────┬─────────────────────────────────────┬───────────────┐
│ NAME           │ VALUE                         │ SCOPE                               │ SOURCE        │
├────────────────┼───────────────────────────────┼─────────────────────────────────────┼───────────────┤
│ api-host       │ https://api.doppler.com       │ C:\Users\User\GitHub\MyProject      │ Config File   │
│ config         │ dev_feature_branch            │ C:\Users\User\GitHub\MyProject      │ Config File   │
│ dashboard-host │ https://dashboard.doppler.com │ C:\Users\User\GitHub\MyProject      │ Config File   │
│ project        │ print_mes                     │ C:\Users\User\GitHub\MyProject      │ Config File   │
│ token          │ dp.ct.UCSp…BQVIT              │ C:\Users\User\GitHub\MyProject      │ Config File   │
│ verify-tls     │ true                          │ /                                   │ Default Value │
└────────────────┴───────────────────────────────┴─────────────────────────────────────┴───────────────┘
┌───────────────┬───────────────────┬──────┐
│ NAME          │ VALUE             │ NOTE │
├───────────────┼───────────────────┼──────┤
│ DATABASE_NAME │ postgres_throught │      │
└───────────────┴───────────────────┴──────┘

```

*Переключаемся в основную ветку*

```bash
bsh ➜  git switch master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
Git checkout to 'master' detected.
Auto-selecting project from repo config file
┌─────────┬───────────┬─────────────────────────────────────┐
│ NAME    │ VALUE     │ SCOPE                               │
├─────────┼───────────┼─────────────────────────────────────┤
│ config  │ dev       │ C:\Users\User\GitHub\MyProject      │
│ project │ print_mes │ C:\Users\User\GitHub\MyProject      │
└─────────┴───────────┴─────────────────────────────────────┘
Successfully switched Doppler to config: dev
┌───────────────┬──────────┬──────┐
│ NAME          │ VALUE    │ NOTE │
├───────────────┼──────────┼──────┤
│ DATABASE_NAME │ postgres │      │
└───────────────┴──────────┴──────┘

bsh ➜  doppler secrets get DATABASE_NAME  --print-config
┌────────────────┬───────────────────────────────┬─────────────────────────────────────┬───────────────┐
│ NAME           │ VALUE                         │ SCOPE                               │ SOURCE        │
├────────────────┼───────────────────────────────┼─────────────────────────────────────┼───────────────┤
│ api-host       │ https://api.doppler.com       │ C:\Users\User\GitHub\MyProject      │ Config File   │
│ config         │ dev                           │ C:\Users\User\GitHub\MyProject      │ Config File   │
│ dashboard-host │ https://dashboard.doppler.com │ C:\Users\User\GitHub\MyProject      │ Config File   │
│ project        │ print_mes                     │ C:\Users\User\GitHub\MyProject      │ Config File   │
│ token          │ dp.ct.User                    │ C:\Users\User\GitHub\MyProject      │ Config File   │
│ verify-tls     │ true                          │ /                                   │ Default Value │
└────────────────┴───────────────────────────────┴─────────────────────────────────────┴───────────────┘
┌───────────────┬──────────┬──────┐
│ NAME          │ VALUE    │ NOTE │
├───────────────┼──────────┼──────┤
│ DATABASE_NAME │ postgres │      │
└───────────────┴──────────┴──────┘

```

Как видим,при переключении ветки git у нас синхронно переключается DATABASE_NAME. 
Этого достаточно, чтобы иметь в каждой ветке git свою ДБ для разработки.
