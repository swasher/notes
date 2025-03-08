# Заметка по itertools

### cycle

Бесконечно повторяет элементы итерируемого объекта. Полезно для циклических паттернов или переключений.

```python
from itertools import cycle
# Пример 1: чередование цветов
colors = cycle(['red', 'blue', 'green'])
# next(colors) -> 'red', 'blue', 'green', 'red'...

# Пример 2: бесконечный статус
states = cycle(['on', 'off'])
# next(states) -> 'on', 'off', 'on', 'off'...

print(next(states))
off
print(next(states))
on
print(next(states))
off
```

### count

Генерирует бесконечную последовательность чисел с заданным стартом и шагом. Поддерживает int и float. Удобно для индексации или генерации чисел. `ids` и `temps` - это итераторы

```python
from itertools import count
# Пример 1: целые числа
ids = count(1, 2)  # Начало 1, шаг 2
print(next(ids))  # 1
print(next(ids))  # 3

# Пример 2: дробные числа (температура)
temps = count(36.6, 0.1)  # Начало 36.6, шаг 0.1
print(next(temps))  # 36.6
print(next(temps))  # 36.7
# Или в цикле:
for temp in temps:
    if temp > 37.0: break
    print(temp)  # 36.8, 36.9, 37.0
```

### repeat

Повторяет один элемент заданное количество раз (или бесконечно, если лимит не указан). Полезно для заполнения или тестов.

```python
from itertools import repeat
# Пример 1: ограниченное повторение
beeps = repeat('beep', 2)

print(next(beeps))
beep
print(next(beeps))
beep
print(next(beeps))   <- ERROR, потому что задано только 2 раза бип!
Traceback (most recent call last):
  File "<input>", line 1, in <module>
StopIteration


# Пример 2: бесконечное повторение
filler = repeat(0)
print(next(filler))  # 0 (и так бесконечно)
```
