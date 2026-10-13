[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn4_characteristic)
Описание:
Are you able to bypass the check? Run the shellcode
`nc 109.233.56.90 11664`
**[characteristic](https://pwn.spbctf.ru/files/shellcoding/characteristic/characteristic)**

Программа генерирует 4 запрещённых байт, которые нельзя использовать, без них необходимо получить шел.
![{A6FD04F9-8496-446F-976A-4EED62AB4EDF}](../../../../z.%20Images/{A6FD04F9-8496-446F-976A-4EED62AB4EDF}.png)
Можно вызывать скрипт до тех пор, пока не повезёт и шел не будет получен.

Шел-код:
![{D5348FB9-B186-4561-B818-948AC090C7BC}](../../../../z.%20Images/{D5348FB9-B186-4561-B818-948AC090C7BC}.png)

Эксплоит:
![{CD80EB22-AFE6-46DC-8DC4-A2D3375ED09C}](../../../../z.%20Images/{CD80EB22-AFE6-46DC-8DC4-A2D3375ED09C}.png)

Запуск:
![Pasted image 20260401230543](../../../../z.%20Images/Pasted%20image%2020260401230543.png)

Ответ: `spbctf{n0_b4d_ch4R5_t0_st0p_y0u}`