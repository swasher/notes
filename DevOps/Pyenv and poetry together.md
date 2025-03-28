# DEPRECATED WORKFLOW

# Pyenv and poetry together

Утилита pyenv позволяет нам использовать различные версии python одновременно в системе, назначая каждому проекту свой интерпретатор. В заметке информация о том, как использовать `pyenv` в связке с `poetry`.

Решение найдено тут → [link](https://medium.com/macoclock/django-setup-in-2022-with-pyenv-poetry-on-macos-b0969830bec8)

Под windows используем пакет [pyenv for Windows](https://github.com/pyenv-win/pyenv-win).

Устанавливаем `pyenv` и `poetry` как написано в документации.

С помощью `pyenv` устанавливаем нужные нам версии питон. При этом `py launcher` *должен* быть удален (или отключен), а сам питон может быть установлен в системе любым способом, `pyenv` его найдет и определит. Но имхо удобнее все версии питона централизовано устанавливать через `pyenv`.

> py launcher - утилита, которая устанавливается вместе win- инсталлятором питона.
> 

Ищем доступные в репозитории версии

```powershell
$ pyenv install -l | findstr 3.1
```

Устанавливаем нужные нам версии в систему

```
$ pyenv install 3.10.2
$ pyenv install 3.5.2
```

В папке проекта устанавливаем версию по-умолчанию

```
$ pyenv local 3.5.2
```

Появится файл `.python-version` c содержимым `3.5.2`

Теперь выполнив команду python в этой папке, у нас автоматически будет использоваться версия 3.5.2:

```
$ python --version
Python 3.10.2
```

Чтобы установить версию по-умолчанию для всех остальных папок, используем global: `pyenv global <version>`

```
C:\\Users\\OLEKSII\\testproject>poetry init
INFO: Could not find files for the given pattern(s).
This command will guide you through creating your pyproject.toml config.
Package name [testproject]:
Version [0.1.0]:
Description []:
Author [None, n to skip]:  n
License []:
Compatible Python versions [^3.11]:  3.5.2  <<<<==== INSERT OUR ACTUAL VERSION

Would you like to define your main dependencies interactively? (yes/no) [yes] no
Would you like to define your development dependencies interactively? (yes/no) [yes] no

Generated file
[tool.poetry]
name = "testproject"
version = "0.1.0"
description = ""
authors = ["Your Name <you@example.com>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "3.5.2"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"

Do you confirm generation? (yes/no) [yes]

```

Теперь обновите `env` у `poetry`, указав pyenv — это очень **важный** шаг. Когда вы установили poetry, она установила python 3.10.5 в качестве системного python, поэтому вам нужно переопределить это, указав правильную env для poetry с помощью `poetry env use $(pyenv which python)`. Вам нужно сделать это только один раз.

```
λ poetry env info

Virtualenv
Python:         3.11.2
Implementation: CPython
Path:           NA
Executable:     NA

System
Platform:   win32
OS:         nt
Python:     3.11.2
Path:       C:\\Users\\OLEKSII\\.pyenv\\pyenv-win\\versions\\3.11.2
Executable: C:\\Users\\OLEKSII\\.pyenv\\pyenv-win\\versions\\3.11.2\\python.exe
                                                    ^^^^^^^^^^ poetry use system python!

λ poetry env use $(pyenv which python)
Creating virtualenv testproject-s9DjT1w_-py3.5 in C:\\Users\\OLEKSII\\AppData\\Local\\pypoetry\\Cache\\virtualenvs
Using virtualenv: C:\\Users\\OLEKSII\\AppData\\Local\\pypoetry\\Cache\\virtualenvs\\testproject-s9DjT1w_-py3.5

λ poetry env info

Virtualenv
Python:         3.5.2
Implementation: CPython
Path:           C:\\Users\\OLEKSII\\AppData\\Local\\pypoetry\\Cache\\virtualenvs\\testproject-s9DjT1w_-py3.5
Executable:     C:\\Users\\OLEKSII\\AppData\\Local\\pypoetry\\Cache\\virtualenvs\\testproject-s9DjT1w_-py3.5\\Scripts\\python.exe
Valid:          True

System
Platform:   win32
OS:         nt
Python:     3.5.2
Path:       C:\\Users\\OLEKSII\\.pyenv\\pyenv-win\\versions\\3.5.2
Executable: C:\\Users\\OLEKSII\\.pyenv\\pyenv-win\\versions\\3.5.2\\python.exe
                                                      ^^^^^^^^^^ now python exec 
                                                                 from virtual env!!!

```

Теперь мы можем создать виртуальное окружение при помощи

```
poetry instrall  <= this command use existing pyproject.toml
OR
poetry install <package-name>
```

## Cheat sheet pyenv for Windows

List all python version available for install

```
pyenv install -l | findstr 3.8
```

Setup python version for current directory (create .python-version)

```
pyenv local 3.8.2
```

## Cheat sheet poetry

Setup poetry in current directory (create `pyproject.toml` config, but not create virtual env yet)

```
poetry init
```

Install all from `pyproject.toml`

```
poetry install
```

Change executable python for current environment (work with pyenv). This command create virt. environment directory.

```
poetry env use $(pyenv which python)
```

If add package manually in `pyproject.toml`

```
poetry lock  # update lock file from pyproject.toml
poetry install
```
