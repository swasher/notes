Источник решения:
https://stackoverflow.com/a/67715630/1334825

Для ScreenSketch:
------------


#### Шаг 1: Проверка наличия приложения

Сначала убедимся, что ScreenSketch зарегистрировано в системе. Откройте PowerShell с правами администратора и выполните:

```powershell
Get-AppxPackage "Microsoft.ScreenSketch" -AllUsers
```


Если вывод есть (вы увидите информацию о пакете, включая PackageFullName), приложение установлено. Переходим к шагу 3.
Если ничего не найдено, попробуем найти его среди всех пакетов:

```powershell
Get-AppxPackage -AllUsers | Where-Object {$_.Name -eq "Microsoft.ScreenSketch"}
```

Запомните значение Name (должно быть Microsoft.ScreenSketch) и PackageFullName.


#### Шаг 2: Перерегистрация приложения


Теперь перерегистрируем ScreenSketch, чтобы обновить его манифест и, возможно, устранить проблему с "verbs". Выполните:

```powershell
Get-AppxPackage "Microsoft.ScreenSketch" -AllUsers | Foreach {Add-AppxPackage -DisableDevelopmentMode -Register "$($_.InstallLocation)\AppXManifest.xml"}
```

`-DisableDevelopmentMode`: Указывает, что мы не в режиме разработчика.
`-Register`: Перерегистрирует приложение, используя его манифест (AppXManifest.xml).
После выполнения проверьте, работает ли ScreenSketch (например, через Win + Shift + S), и запустите вашу команду Django снова:

```bash
python manage.py makemigrations
```

Если предупреждение для ScreenSketch исчезло — отлично, проблема решена.

> **ЭТОТ СПОСОБ У МЕНЯ СРАБОТАЛ - НО ТОЛЬКО ДО ПЕРЕЗАГРУЗКИ!!!**


#### Шаг 3: Если перерегистрация не помогла

Если предупреждение осталось, можно попробовать полностью переустановить ScreenSketch:

Удалите приложение:

```powershell
Get-AppxPackage "Microsoft.ScreenSketch" -AllUsers | Remove-AppxPackage
```

Установите заново через Microsoft Store:

- Откройте Microsoft Store, найдите "Snipping Tool" (это современное название ScreenSketch) и установите.
- Проверьте снова с python manage.py makemigrations.

> **ЭТОТ СПОСОБ ПОМОГ - ОШИБКА ИСЧЕЗЛА И ПОСЛЕ ПЕРЕЗАГРУЗКИ НЕ ПОЯВИЛАСЬ!!!**  
** UPDATE ** - все равно появляется ошибка...

PS Установка ScreenSketch 

    Get-AppxPackage -AllUsers Microsoft.ScreenSketch | Foreach {Add-AppxPackage -DisableDevelopmentMode -Register "$($_.InstallLocation)\AppXManifest.xml"}

# ВСЕ ЭТО НЕ ПОМОГАЕТ!

### Единственный реальный способ!

- Определить, что за библиотека вызывает это поведение. В моем случае это оказался weasyprint. Обычно это библиотеки с внешними бинарными зависимостями.
- Перенести импорт библиотек с верхнего уровня во внутрь функций:

Было:

```python
from weasyprint import HTML, CSS

def order_print(request, orderid=None):
    pdf_file = HTML(string=html_template)\
    .write_pdf(stylesheets=[
        CSS(string='@page { size: A4; margin: 1cm }'),
        'static/css/order_report.css',
])
```

Стало:

```python
def order_print(request, orderid=None):
    from weasyprint import HTML, CSS

    pdf_file = HTML(string=html_template)\
    .write_pdf(stylesheets=[
        CSS(string='@page { size: A4; margin: 1cm }'),
        'static/css/order_report.css',
])
```

Теперь этот импорт не производится при таких действиях, как запуск проекта, выполнение миграций и тому подобное.
