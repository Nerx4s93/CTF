[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn4_rop_rop)
Описание:
Can you pwn it?
`nc 109.233.56.90 11653`
**[task](https://pwn.spbctf.ru/files/shellcoding/rop_rop/task)**  
**[libc-2.31.so](https://pwn.spbctf.ru/files/shellcoding/rop_rop/libc-2.31.so)**

Алгоритм создания сегмента данных и записи шел-кода такой же как и в [call_fcn_rop](call_fcn_rop.md). Необходимо получить шел, что можно сделать через pwntools.

Солвер:
![Pasted image 20260404135037](../../../../z.%20Images/Pasted%20image%2020260404135037.png)

Запуск:
![{42239CF9-EC64-43E0-B56A-C73A900E6754}](../../../../z.%20Images/{42239CF9-EC64-43E0-B56A-C73A900E6754}.png)

Ответ: `spbctf{cbd34143c83ff5c8a80ff884e72c32a737a7df91}`