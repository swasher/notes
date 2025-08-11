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

Цветной промпт.
----------------------------------

```bash
# Включаем цветное приглашение
export PS1='\[\e[32m\]\u@\h:\[\e[33m\]\w\[\e[0m\]\$ '
```

[![asciicast](https://asciinema.org/a/12345.png)](https://asciinema.org/a/12345)
