General info about integration is here

[link](https://github.com/cmderdev/cmder/wiki/Seamless-IntelliJ-based-Integration)


### Cmder

```
cmd.exe /k "%CMDER_ROOT%\vendor\init.bat"
cmd.exe /k "%CMDER_ROOT%\vendor\bin\cmder_shell.cmd"
```

не знаю, что правильнее, работает и то и то

### Cmder + powershell

```
cmd.exe /c pwsh.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -NoExit -Command "Invoke-Expression '. ''%CMDER_ROOT%/vendor/profile.ps1'''"
```

### Cmder + ConEmu-Maximus5

```
"cmd.exe" /k set ConEmuDir=""%CMDER_ROOT%"\vendor\conemu-maximus5&&Call %CMDER_ROOT%\vendor\init.bat"
```

### Cmder + bash



```
cmd.exe /c  ""%CMDER_ROOT%\vendor\git-for-windows\bin\bash.exe" --login -i"
```

В этом варианте работают такие утилиты, как ssh-copy-id

### bash

*Default for now*

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
 
### Prompt

В темной теме приглашение cmder выглядит как черное на темно-сером, исправляем:

Файл `$CMDER_ROOT/config/cmder_prompt_config.lua`

ищем в самом конце раздел

```
-- Prompt Element Colors
uah_color = "\x1b[1;33;49m" -- Green = uah = [user]@[hostname]
cwd_color = "\x1b[1;32;49m" -- Yellow cwd = Current Working Directory
lamb_color = "\x1b[1;32;49m" -- Light Grey = Lambda Color
clean_color = "\x1b[37;1m"
dirty_color = "\x1b[33;3m" -- Yellow, Italic
conflict_color = "\x1b[31;1m" -- Red, Bold
unknown_color = "\x1b[37;1m" -- White = No VCS Status Branch Color
```

Читаем тут `https://github.com/cmderdev/cmder/wiki/Customization`, там есть табличка с цветовыми кодами, меняем, например, на голубой:

```
lamb_color = "\x1b[1;30;49m"
                      ↓
lamb_color = "\x1b[1;34;49m"
```

Было:

![image](https://github.com/swasher/notes/assets/1525918/ee805cad-2d6a-4be6-b0a0-d68de520f78a)

Стало:

![image](https://github.com/swasher/notes/assets/1525918/6375170a-863b-4fad-bf3a-f427809873f5)
