<details><summary>Scoop vs choco vs winget</summary>
 
**winget**
- устанавливает пакеты из windows store через командную строку

**scoop**
- устанавливает в свою папку ~/scoop/
- больше предназначен для инструментов разработчика и утилит, нежели больших программ как chrome или skype

**choco**
- устанавливает софт в `Program Files`, требует права администратора

</details>

python
-----------
#### - если нужна одна версия python
используем `winget`
#### - если нужно несколько версий python
используем [pyenv-win](https://github.com/pyenv-win/pyenv-win)

### winget

<details><summary>Если winget не работает на свежей системе</summary>
Это известная проблем, решение такое
 
    Invoke-WebRequest -Uri https://aka.ms/getwinget -OutFile winget.msixbundle
    Add-AppxPackage winget.msixbundle
    del winget.msixbundle
</details>
Список доступных версий

    winget search --id Python.Python

Установка (терминал должен быть админских прав)

    winget install -e --id Python.Python.3.11 --scope machine

где
- -e - exact, искать пакет в точности включая регистр
- -id - ID пакета
- --scope machine - устанавливать в Program Files (без ключа в юзера, в `C:\Users\<YourUser>\AppData\Local\Programs\Python\Python311`)

PATH включается автоматически, нужно только перезапустить терминал.

### pyenv-win

*TODO*
  
pipx
-----------

Удобно использовать для cli-приложений, устанавливающихся глобально.

#### Install
Устанавливается при помощи scoop:

    scoop install pipx
    pipx ensurepath

#### Updade

    scoop update pipx

#### Path

Пути про писываются автомтически командой `pipx ensurepath`

poetry
-----------

    pipx install poetry 
    pipx upgrade poetry


 make
 -----------

Install it with `choco`:

```
choco install make
```
