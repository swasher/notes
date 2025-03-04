### bash

*Default shell for now, instead cmder*

```
"C:\Program Files\Git\bin\bash.exe" --login -i
```

В этом варианте работают такие утилиты, как ssh-copy-id.

`--login`: Загружает профиль (~/.bash_profile или .bashrc).  
`-i`: Интерактивный режим для поддержки автодополнения.

В пользовательской директории сделай файл `.bash_profile`:

    if [ -f ~/.bashrc ]; then
      . ~/.bashrc
    fi

Там же сделай `.bashrc`. Например, чтобы загружать completions из папки `.bash_completion.d`:

    if [ -d ~/.bash_completion.d ]; then
        for script in ~/.bash_completion.d/*; do
            . "$script"
        done
    fi

Внимание! Важно, чтобы файлы не были в кодировке UTF-8-BOM, тогда bash выдаст ошибку. Проверить и преобразовать можно с помощью Notepad++.
