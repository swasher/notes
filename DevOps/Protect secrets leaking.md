## Установка
Устанавливаем [gitleaks](https://github.com/gitleaks/gitleaks) или, если когда нибудь появятся prebuild binaries for windows, [ripsecrets](https://github.com/sirwart/ripsecrets)

## Hook

В файле `.git/hooks/pre-commit` пишем (с учетом, что работаем с git bash)

```
#!/bin/sh
gitleaks git --pre-commit --redact --staged --verbose
```
