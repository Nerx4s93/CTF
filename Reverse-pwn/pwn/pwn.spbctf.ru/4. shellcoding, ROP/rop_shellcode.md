[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn4_rop_shellcode)
Описание:
Run the shellcode and call /bin/getflag
`nc 109.233.56.90 11652`
**[task](https://pwn.spbctf.ru/files/shellcoding/rop_shellcode/task)**  
**[libc-2.31.so](https://pwn.spbctf.ru/files/shellcoding/rop_shellcode/libc-2.31.so)**

Алгоритм создания сегмента данных и записи шел-кода такой же как и в [call_fcn_rop](call_fcn_rop.md). Только шел-код надо поменять.

Шел-код:
![Pasted image 20260404134400](../../../../z.%20Images/Pasted%20image%2020260404134400.png)

Эксплоит:
![{56A16530-060F-4D96-8073-7C4A3DFBD182}](../../../../z.%20Images/{56A16530-060F-4D96-8073-7C4A3DFBD182}.png)

Запуск:
![{3BF63A6D-869D-4BEF-889A-29CEED081992}](../../../../z.%20Images/{3BF63A6D-869D-4BEF-889A-29CEED081992}.png)

Ответ: `spbctf{765bd191ffe495409b5644e1c7928b031f1d9d5a}`