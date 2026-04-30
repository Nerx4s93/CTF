[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn4_call_fcn_small)
Описание:
Just call win_fcn and make it print flag for you. oh, and now we limited size of your shellcode!
`nc 109.233.56.90 11650`
**[task](https://pwn.spbctf.ru/files/shellcoding/call_fcn_small/task)**

Имеется всего 27 байт для шел-кода.
![Pasted image 20260401121820](../../../../z.%20Images/Pasted%20image%2020260401121820.png)

27 байт не хватит, чтобы вызвать функцию с таким количеством параметров.
![Pasted image 20260401121851](../../../../z.%20Images/Pasted%20image%2020260401121851.png)

Поэтому необходимо сначала вызвать `sys_read`, чтобы считать больше байт, а затем уже вызвать функцию.

Вызов `sys_read`:
![{946F45EB-A0A3-45F3-8460-5465D55CAF78}](../../../../z.%20Images/{946F45EB-A0A3-45F3-8460-5465D55CAF78}.png)

Вызов функции:
![Pasted image 20260401122052](../../../../z.%20Images/Pasted%20image%2020260401122052.png)
Команды `sub` тут не просто так, если передавать напрямую '0x80', то неизбежно будет нуль-байт и считывание закончится, т.к. `sys_read` читает до первого нуль-байта.

Эксплоит:
![Pasted image 20260401122147](../../../../z.%20Images/Pasted%20image%2020260401122147.png)

Запуск:
![{E6E4EC82-66D1-46E0-9BF3-209ADB8C8A7A}](../../../../z.%20Images/{E6E4EC82-66D1-46E0-9BF3-209ADB8C8A7A}.png)

Ответ: `spbctf{17e5fc46c25360bed448927dd76548a122517d46}`