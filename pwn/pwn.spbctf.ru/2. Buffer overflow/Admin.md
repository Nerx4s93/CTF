[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_admin)
Описание: Надо сделать переполнение переменной username в bss секции и стать админом.  
[task](https://pwn.spbctf.ru/files/overflow/admin/task
nc 109.233.56.90 11575

![{D05A7754-B894-48E2-B31D-E6C75BA774D2}](../../../y.%20Images/{D05A7754-B894-48E2-B31D-E6C75BA774D2}.png)
![{882AFB5F-9D7A-4390-B36C-A95CC5E53E63}](../../../y.%20Images/{882AFB5F-9D7A-4390-B36C-A95CC5E53E63}.png)

Необходимо просто ввести 257 символ, чтобы в is_admin лежало ненулевое значение.
![{98B9E773-E9AA-40C5-8562-ED9DF3935F30}](../../../y.%20Images/{98B9E773-E9AA-40C5-8562-ED9DF3935F30}.png)

Ответ: `spbctf{this_is_simple_overflow}`