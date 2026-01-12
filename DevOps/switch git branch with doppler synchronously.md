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

