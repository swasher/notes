Git hook under window and Github Desktop
-------------------------------

Для того, чтобы что-то работало, нужно использовать `sh` вместо `bash`.

```sh
#!/bin/sh

bump-my-version.exe bump patch --commit --tag

# Убедитесь, что команда завершилась успешно
if [ $? -ne 0 ]; then
  echo "Ошибка при выполнении bump-my-version"
  exit 1
fi

# Продолжить выполнение git push
exit 0
```

Помещаем код в файл `pre-push` (после коммита, но перед пушем) или `pre-commit` (перед коммитом - обычно нам нужно это) в `project\.git\hooks`
