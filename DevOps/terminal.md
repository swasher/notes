Устанавливаем
-------------------------

[`git for windows`](https://gitforwindows.org/)

В pycharm
--------------------------

`"C:\Program Files\Git\bin\bash.exe" --login -i`

История команд
-----------------------------

Хранится в `%USERPROFILE%\.bash_history`

Настройки терминала
------------------------------

### Общая информация

Профили хранятся в файлах `%USERPROFILE%\.bash_profile` и `%USERPROFILE%\.bashrc`

`.bash_profile`:	Логин-сессии (когда Git Bash запускается как интерактивная оболочка входа).  

`.bashrc`:	Не-логин-сессиях (например, при запуске нового окта терминала внутри существующей сессии).  

👉 Важно для Windows: Git Bash по умолчанию запускается как логин-оболочка (login shell), поэтому первым выполняется .bash_profile.  

### Что в них помещать?

| Файл           | Рекомендуемое содержимое                                                                 |
|----------------|-----------------------------------------------------------------------------------------|
| `.bash_profile`| - Настройки, которые должны выполняться один раз за сессию (например, экспорт переменных PATH, EDITOR).<br>- Вызов `.bashrc` (если нужны его настройки). |
| `.bashrc`      | - Настройки для каждого нового интерактивного окна терминала:<br>  • Алиасы (`alias ll='ls -al'`)<br>  • Функции оболочки<br>  • Пользовательский PS1 (prompt)<br>  • Параметры `set`, `shopt`. |

### Практика в Git Bash для Windows

Если ничего специально не сделать, сам по себе выполняется только `.bash_profile`. Поэтому в него пишем такой код, для подгрузки `.bashrc`:

```bash
echo Echo from bash_profile
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi
```

`echo` нужно для того, чтобы мы при запуске терминала видели, какие файлы настроек точно загрузились.

Autocomplete
----------------------------------

в `.bashrc` прописываем:

```bash
COMPLETION_DIR="$HOME/.bash_completion.d"

if [ -d "$COMPLETION_DIR" ]; then
    for file in "$COMPLETION_DIR"/*; do
        [[ -f "$file" ]] && source "$file"
    done
fi
```

В папку `.bash_completion.d` помещаем скрипты автодополнений. Например, самописный скрипт для Makefile:

```bash
_make() {
    local cur=${COMP_WORDS[COMP_CWORD]}
    local cache_dir="$HOME/.bash_completion.d/cache"
    local cache_file="$cache_dir/make_targets_$(pwd | sed 's|/|_|g')"

    mkdir -p "$cache_dir"

    if [ -f Makefile ]; then
        # Если кэш устарел или отсутствует, обновляем
        if [ ! -f "$cache_file" ] || [ Makefile -nt "$cache_file" ]; then
            make -qp 2>/dev/null | awk '/^[a-zA-Z0-9][^\/[:space:]]*:([^=]|$)/ {split($1,a,":");print a[1]}' > "$cache_file"
        fi
        COMPREPLY=( $(compgen -W "$(cat "$cache_file")" -- "$cur") )
    fi
}
complete -F _make make
```

Обычно эти скрипты создаются самими приложениями. 

History
------------------------------------

Если терминал закрывать "крестиком", история может не сохраняться. Поэтому добавляем команды:

```bash
# Убедитесь, что история сохраняется правильно
HISTFILE=~/.bash_history
HISTFILESIZE=10000
HISTSIZE=10000

# Не пропускать дубликаты и команды с пробелом в начале
HISTCONTROL=ignoredups:erasedups

# Сохранять историю после каждой команды (а не только при выходе)
PROMPT_COMMAND='history -a'
```


Цветной кастомный промпт.
----------------------------------

```bash
# Включаем цветное приглашение
export PS1='\[\e[32m\]\u@\h:\[\e[33m\]\w\[\e[0m\]\$ '
```

| Цвет      | Пример текста       |
|-----------|---------------------|
| Зелёный   | `user@host`         |
| Жёлтый    | `:/path/to/folder$` |

Это не нужно, если мы используем Starship, но без него - обязательно.

Starship (для красивого и быстрого prompt’а)
-----------------------

Весь функционал — это модули, которые динамически показываются в промпте только когда они нужны (например, версия Python — только если ты в проекте с pyproject.toml или .py файлами). Таких модулей очень много для разных утилит - есть для git, Azure, python, ruby, vagrant - и десятков других утилит и языков программировния. Есть, к примеру, модуль Local IP, который отображает ip в промпте.

Я устанавливаю через `scoop install starship`.

После установки нужно создать файл `~/.config/starship.tomp` и поместить в него простой конфиг (пример с офф. сайта, я добавил секцию git):

```toml
# Get editor completions based on the config schema
"$schema" = 'https://starship.rs/config-schema.json'

# Inserts a blank line between shell prompts
add_newline = true

# Replace the '❯' symbol in the prompt with '➜'
[character] # The name of the module we are configuring is 'character'
success_symbol = '[➜](bold green)' # The 'success_symbol' segment is being set to '➜' with the color 'bold green'

# Disable the package module, hiding it from the prompt completely
[package]
disabled = true

[git_branch]
symbol = "🌿 "
[git_status]
staged = "[+](green)"
untracked = "[?](red)"
modified = "[*](yellow)"
```

Затем нужно сделать 

```bash
echo 'eval "$(starship init bash)"' >> ~/.bashrc
```

и перезапустить терминал.
