Для вот такой структуры папок

```
my_project/
├── my_module
│   ├── myprogram.py
├── tests/
│   ├── test_my_module.py
└── pyproject.toml
├── main.py
```

мне понадобилось добавить в терминал переменную окружения `PYTHONPATH=.`, чтобы `coverage` смог найти модуль `my_module`, выполняя тест `test_my_module.py`. При это самому pytest достаточно настройки в `pyproject.toml`:

```toml
[tool.pytest.ini_options]
pythonpath = ["."]
```

Оценку покрытия запускал такой командой: `pytest --cov=. --cov-report=html tests/`

Это делается в `Tools` -> `Termninal` -> `Environment variables`
