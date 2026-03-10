[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_sleep)
Описание:
Вы получите флаг через 1024 секунд, осталось чуть чуть подождать.  
[task](https://pwn.spbctf.ru/files/overflow/sleep/task)
nc 109.233.56.90 11576

Необходимо переполнить буфер и переписать значение seconds на 0.
![Pasted image 20260309151056](../../../Z.%20Images/Pasted%20image%2020260309151056.png)

Солвер:
![{693469EA-93ED-4AE8-B406-2E140784B083}](../../../Z.%20Images/{693469EA-93ED-4AE8-B406-2E140784B083}.png)

Запуск:
![{E16EE162-0FA7-4405-86B4-ABB77B1D4433}](../../../Z.%20Images/{E16EE162-0FA7-4405-86B4-ABB77B1D4433}.png)

Ответ: `spbctf{beep_boop_overflow_int_variable}`