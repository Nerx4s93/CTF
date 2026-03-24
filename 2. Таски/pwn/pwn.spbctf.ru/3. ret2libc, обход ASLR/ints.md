[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_ints)
Описание:
Server:
`nc -nv 109.233.56.90 11635`
Binary: [**task**](https://pwn.spbctf.ru/files/aslr/ints/task_simple)
libc:[libc](https://pwn.spbctf.ru/files/aslr/ints/libc-2.31.so)
[ld](https://pwn.spbctf.ru/files/aslr/ints/ld-2.31.so)

В задаче необходимо в первый элемент массива записать `bin/sh\x00\x00`, затем заполнить весь массив, переполнить на 2 элемента и записать в `function` ссылку на функцию `system` из `libc`.
![{9554C3DB-36B3-425F-9D1A-A0A5DD36D520}](../../../../z.%20Images/{9554C3DB-36B3-425F-9D1A-A0A5DD36D520}.png)
![{72E95765-D558-4C15-949F-3CFBB0E10D7C}](../../../../z.%20Images/{72E95765-D558-4C15-949F-3CFBB0E10D7C}.png)

ASLR отключён, все адреса предоставлены.
![Pasted image 20260324234001](../../../../z.%20Images/Pasted%20image%2020260324234001.png)

Солвер:
![Pasted image 20260324234018](../../../../z.%20Images/Pasted%20image%2020260324234018.png)

Запуск:
![{48B440F2-2965-4C96-9840-D90A30BF4F07}](../../../../z.%20Images/{48B440F2-2965-4C96-9840-D90A30BF4F07}.png)

Ответ: `spbctf{4003a075a33db2bf0984a700bf8740cd0e39441d1120762f4139dbe9da795ebc}`