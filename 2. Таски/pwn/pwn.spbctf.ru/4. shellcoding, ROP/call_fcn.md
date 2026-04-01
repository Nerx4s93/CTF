[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn4_call_fcn)
Описание:
Just call win_fcn and make it print flag for you
`nc 109.233.56.90 11668`
**[task](https://pwn.spbctf.ru/files/shellcoding/call_fcn/task)**

Программа читает 0x400 байт в сегмент памяти с правами RWX, а затем исполняет введённый код.
![](../../../../z.%20Images/Pasted%20image%2020260331190538.png)

Шел-код:
![](z.%20Images/{2AC48960-F96D-4021-8B24-9477345A6430}.png)

Солвер:
![](z.%20Images/{069819EF-10AE-4F36-8D69-143109E3F79F}.png)

Запуск:
![](z.%20Images/{AC7C8C8F-48A2-464E-94AB-CEA20F69A07E}.png)

Ответ: `spbctf{w3l1_d0n3_e1be468edfa56b6370f0918dbdfc4170}`