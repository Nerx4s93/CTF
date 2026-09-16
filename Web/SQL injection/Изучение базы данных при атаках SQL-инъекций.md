Чтобы эксплуатировать уязвимости SQL-инъекций, часто необходимо найти информацию о базе данных. Это включает:
- Тип и версию программного обеспечения базы данных.
- Таблицы и столбцы, которые содержит база данных.

## Запрос типа и версии базы данных

Для определения версии базы данных:

| Тип базы данных  | Запрос                    |
| ---------------- | ------------------------- |
| Microsoft, MySQL | `SELECT @@version`        |
| Oracle           | `SELECT * FROM v$version` |
| PostgreSQL       | `SELECT version()`        |
[Lab 7. Атака SQLi, запрос типа и версии базы данных на MySQL и Microsoft](Labs/Lab%207.%20Атака%20SQLi,%20запрос%20типа%20и%20версии%20базы%20данных%20на%20MySQL%20и%20Microsoft.md)

## Просмотр содержимого базы данных

Большинство типов баз данных (кроме Oracle) имеют набор представлений (информационной схемой). Она предоставляет информацию о базе данных.

Можно выполнить запрос к `information_schema.tables`, чтобы получить список таблиц в базе данных:
```sql
SELECT * FROM information_schema.tables
```

Это вернёт результат, подобный следующему:
```
TABLE_CATALOG  TABLE_SCHEMA  TABLE_NAME  TABLE_TYPE
=====================================================
MyDatabase     dbo           Products    BASE TABLE
MyDatabase     dbo           Users       BASE TABLE
MyDatabase     dbo           Feedback    BASE TABLE
```

Затем можно выполнить запрос к `information_schema.columns`, чтобы получить список столбцов в отдельных таблицах:
```sql
SELECT * FROM information_schema.columns WHERE table_name = 'Users'
```

Это вернёт результат, подобный следующему:
```
TABLE_CATALOG  TABLE_SCHEMA  TABLE_NAME  COLUMN_NAME  DATA_TYPE
=================================================================
MyDatabase     dbo           Users       UserId       int
MyDatabase     dbo           Users       Username     varchar
MyDatabase     dbo           Users       Password     varchar
```

[Lab 8. SQLi, просмотр содержимого базы данных в базах данных, отличных от Oracle](Labs/Lab%208.%20SQLi,%20просмотр%20содержимого%20базы%20данных%20в%20базах%20данных,%20отличных%20от%20Oracle.md)