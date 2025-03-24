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
