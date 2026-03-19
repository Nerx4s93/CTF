[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_printfer)
Описание:
Hi! This is your [binary](https://pwn.spbctf.ru/files/canary/task_printfer)  
nc 109.233.56.90 11589

При помощи уязвимости форматной строки необходимо считать канарейку, а затем переполнить буфер message, переписав адрес возврата.
![Pasted image 20260316202249](../../../../z.%20Images/Pasted%20image%2020260316202249.png)

При помощи двух вызовов можно определить место нахождение канарейки - 23.
![{E6EB30B0-69BD-419E-B197-8A18F7C0DF82}](../../../../z.%20Images/{E6EB30B0-69BD-419E-B197-8A18F7C0DF82}.png)

Солвер:
![{5ABA28F3-031E-442F-9BB9-7CC8B62C4420}](../../../../z.%20Images/{5ABA28F3-031E-442F-9BB9-7CC8B62C4420}.png)

Запуск:
![{6FE12C91-0C86-4608-A7A2-6EDEA6F3BF67}](../../../../z.%20Images/{6FE12C91-0C86-4608-A7A2-6EDEA6F3BF67}.png)

Ответ: `spbctf{d1aa853d26a71d8ca21f771f018a3267235d8359785881a718d6a022ad7c46f0}`