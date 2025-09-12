uv
========

`uv` полностью может заменить все инструменты. Он умеет:

- управлять виртуальной средой, как `poetry`
- устанавливать глобальные пакеты, как `pipx`
- управлять версиями python, как `pyenv`

Установка
----------------

    # On Windows.
    powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

Обновление

    uv self update


QuickStart
-----------------
    
    $ uv init
    $ uv add django
    $ uv add ruff --dev
    $ uv run ./manage.py

uv вместо pipx
-------------------

    $ uv tool install rust-just
    $ just
    
    $ uv tool install cowsay
    $ cowsay mooo

Так же есть возможность запускать исполнение вообще без установки пакетов:

    $ uvx cowsay mooo
    $ uvx ruff check   # Lint all files in the current directory.
    $ uvx ruff format  # Format all files in the current directory.

Утилиты устанавливаеются в `%HOME%\.local\.bin`

Управление версиями Python для проектов
-----------------

Вы просто настраиваете свой проект на использование определенной версии Python (указывая номер версии в файле `.python-version`), а затем всякий раз, когда вы используете его uv runв проекте, устанавливается правильная версия Python, создается виртуальная среда и устанавливаются зависимости. 

Когда вы хотите обновить версию Python, вы просто редактируете `.python-version`, и все: когда вы запускаете любую команду, `uv run` новая версия Python автоматически загружается и используется.

uv может использовать имеющиеся в системе инстелляции python (system) или свои собственные (managed).

Создает .python-version в проекте, тем самым определив версию питон для конкретного проекта

    uv python pin 3.11

Специально устанавливать питон для проекта не нужно, он установится автоматом, если будет необходимость.


Управление глобальным python
----------------------------------

Установить несколько версий:

    $ uv python install 3.10 3.11 3.12

Мы может увидеть, какая версия установлена и какие есть доступные версии для загрузки:

    $ uv python list
    cpython-3.13.1+freethreaded-windows-x86_64-none    <download available>
    cpython-3.13.1-windows-x86_64-none                 <download available>
    cpython-3.12.8-windows-x86_64-none                 AppData\Roaming\uv\python\cpython-3.12.8-windows-x86_64-none\python.exe
    cpython-3.11.11-windows-x86_64-none                <download available>
    cpython-3.10.16-windows-x86_64-none                <download available>
    cpython-3.9.21-windows-x86_64-none                 <download available>
    cpython-3.8.20-windows-x86_64-none                 <download available>
    cpython-3.7.9-windows-x86_64-none                  <download available>
    pypy-3.10.14-windows-x86_64-none                   <download available>
    pypy-3.9.19-windows-x86_64-none                    <download available>
    pypy-3.8.16-windows-x86_64-none                    <download available>
    pypy-3.7.13-windows-x86_64-none                    <download available>

Чтобы увидеть, куда питон установился через uv, а так же все остальные доступные установленные экземпляры питон:

    $ uv python find
    C:\Users\swasher\AppData\Roaming\uv\python\cpython-3.12.8-windows-x86_64-none\python.exe

После установки питон не будет доступен в командной строке, так его нет в PATH. Чтобы добавить конкретную версию python в PATH, нужно выполнить следующую команду, после которой питон можно запустить как `python3.12` (потому что в `~/.local/.bin` появится `python3.12.exe` (не линком!)

    $ uv python install 3.12 --preview

А чтобы питон запускался как просто `python`, нужно какую-то версию назначить по-умолчанию (тогда в `~/.local/.bin` появятся `python.exe` и  `python3.exe`)

    $ uv python install 3.12 --default --preview

Если `~/.local/.bin` нет в PATH, то ее можно добавить командой

    $ uv tool update-shell



Группы
------------------

Добавление пакетов в разные группы (без их установки - --no-sync):

    $ uv add django
    $ uv add --group dev --no-sync ruff
    $ uv add --group prod --no-sync gunicorn

Но если запустить что-то в среде, uv установит недостающие пакеты из всех групп. Согласно [статье](https://www.loopwerk.io/articles/2024/python-uv-revisited/), нужно добавить в `pyproject.toml` строки:

    [tool.uv]
    default-groups = []

И теперь, чтобы установить группу, нужно выполнить

    $ uv sync --group prod
    $ uv sync --group dev --group prod

Показ устаревших пакетов
-------------------------

    $ uv tree --outdated
    $ uv tree --outdated --depth=1

Это покажет дерево всех зависимостей с указанием их последней версии, если доступно обновление. Вторая команда показывает только зависимости верхнего уровня, те, которые действительно интересны.

uv позволяет легко увидеть устаревшие пакеты во всех группах, даже если эти группы не установлены:

    $ uv tree --group prod --group dev --outdated --depth=1

Если мы не используем группы, то можно воспользоваться командой `uv pip list --outdated`, которая показывает *только* устаревшие пакеты (но это не работает с группами)

Обновление пакета
----------------------

    uv lock --upgrade-package <package>

Но для этого в pyproject.toml должно быть "разрешено" устанавливать более высокие версии. Например, если указано "django==4.1.7", то пакет не обновится. Нужно указать "django>=4.1.7".
