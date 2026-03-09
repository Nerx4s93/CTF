[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_indexes)
Описание: Hi! I created very secure [system](https://pwn.spbctf.ru/files/overflow/indexes) to store my secrets. Can you bypass it?
nc 109.233.56.90 11572

Чтобы получить флаг secretPtr должен быть равен s2.
![{97CA33BA-CBB0-485B-B5C0-11D366A8BBF5}](../../../Z.%20Images/{97CA33BA-CBB0-485B-B5C0-11D366A8BBF5}.png)

Из массива global можно добраться до массива s2.
![Pasted image 20260309123053](../../../Z.%20Images/Pasted%20image%2020260309123053.png)
Индекс = 16640 / 8 = 2080.

Проверка:
![{89E2D32D-00BC-42D1-948F-DA5DF68F165F}](../../../Z.%20Images/{89E2D32D-00BC-42D1-948F-DA5DF68F165F}.png)

Ответ: `spbctf{well_I_should_not_forget_to_check_indexes}`