[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn4_no_syscall)
Описание:
test
`nc 109.233.56.90 11614`
**[no_syscall](https://pwn.spbctf.ru/files/shellcoding/no_syscall/no_syscall)**

В шел-коде запрещён вызов команды `syscall`.
![{8E9B65FC-460B-4212-9FE3-DA141DD9F497}](../../../../z.%20Images/{8E9B65FC-460B-4212-9FE3-DA141DD9F497}.png)
Это легко обойти, внутри сегмента памяти куда читается шел-код можно создать новую область памяти и вызвать `read` ещё раз, а в этот `read` подать уже шел-код вызова шела.

Шел-код:
![Pasted image 20260402235902](../../../../z.%20Images/Pasted%20image%2020260402235902.png)

Солвер:
![Pasted image 20260403000112](../../../../z.%20Images/Pasted%20image%2020260403000112.png)

Запуск:
![{2562F99B-0E98-4B0A-BE10-5625D987A9FE}](../../../../z.%20Images/{2562F99B-0E98-4B0A-BE10-5625D987A9FE}.png)

Ответ: `spbctf{808a525795f7715f8aade2c213dad440}`