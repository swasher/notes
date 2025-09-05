🔹 psql — работа с базой
----------------------------

```sh
# Подключиться к базе
psql -U user -h host -p port dbname

# Выполнить SQL-скрипт из файла
psql -U user -d dbname -f script.sql

# Выполнить одноразовый SQL-запрос
psql -U user -d dbname -c "SELECT * FROM users LIMIT 10;"

# Список баз
psql -U user -l

# Список таблиц внутри базы
psql -U user -d dbname -c "\dt"

# Список пользователей
psql -U user -d dbname -c "\du"

# Экспорт таблицы в CSV (через psql)
psql -U user -d dbname -c "\COPY users TO 'users.csv' CSV HEADER"

# Импорт CSV в таблицу
psql -U user -d dbname -c "\COPY users FROM 'users.csv' CSV HEADER"
```

🔹 pg_dump — создание дампов
---------------------------

```sh
# Полный дамп (SQL-скрипт, можно открыть в редакторе)
pg_dump -U user -h host -Fp dbname > dump.sql

# Полный дамп в формате "custom" (бинарный, для pg_restore)
pg_dump -U user -h host -Fc dbname > dump.dump

# Дамп только схемы (структура без данных)
pg_dump -U user -h host -s dbname > schema.sql
# то же самое
pg_dump -U user -h host --schema-only dbname > schema.sql

# Дамп только данных (без структуры)
pg_dump -U user -h host -a dbname > data.sql
# то же самое
pg_dump -U user -h host --data-only dbname > data.sql

# Дамп одной таблицы
pg_dump -U user -h host -t users dbname > users.sql

# Дамп с исключением таблиц
pg_dump -U user -h host --exclude-table=logs dbname > dump.sql

# Дамп с игнорированием владельцев и прав
pg_dump -U user -h host --no-owner --no-privileges -Fc dbname > dump.dump
```

🔹 pg_restore — восстановление из pg_dump -Fc
-----------------------------------

```sh
# Восстановить дамп в существующую базу
pg_restore -U user -d newdb dump.dump

# Восстановить с удалением объектов перед созданием (DROP …)
pg_restore -U user -d newdb --clean dump.dump

# Восстановить с созданием базы (если дамп содержит CREATE DATABASE)
pg_restore -U user --create -d postgres dump.dump

# Игнорировать владельцев (полезно при переносе между серверами)
pg_restore -U user -d newdb --no-owner dump.dump

# Восстановить только схему
pg_restore -U user -d newdb --schema-only dump.dump

# Восстановить только данные
pg_restore -U user -d newdb --data-only dump.dump

# Восстановить только одну таблицу
pg_restore -U user -d newdb -t users dump.dump

# Параллельное восстановление (ускорение на больших дампах)
pg_restore -U user -d newdb -j 4 dump.dump
```

🔹 Быстрые сценарии
-------------------------

```sh
# ✅ Сделать полный дамп (custom формат)
pg_dump -U postgres -Fc mydb > mydb.dump

# ✅ Развернуть дамп в новую базу
createdb -U postgres newdb
pg_restore -U postgres -d newdb --no-owner mydb.dump

# ✅ Копия базы "на лету" через pipe
pg_dump -U postgres -Fc olddb | pg_restore -U postgres -d newdb
```
