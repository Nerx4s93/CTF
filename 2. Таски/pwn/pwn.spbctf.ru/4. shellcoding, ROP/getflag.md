[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn4_getflag)
Описание:
Run the shellcode and call /bin/getflag
`nc 109.233.56.90 11667`
**[task](https://pwn.spbctf.ru/files/shellcoding/getflag/task)**

Программа читает 0x400 байт в сегмент памяти с правами RWX, а затем исполняет введённый код.
![](../../../../z.%20Images/Pasted%20image%2020260331190500.png)

При помощи утилиты [str2stack](https://github.com/Nerx4s93/str2stack) перевожу строку `/bin/getflag` в байты.
![{E7EEC35F-1F32-4518-99E5-37CF954B2D27}](../../../../z.%20Images/{E7EEC35F-1F32-4518-99E5-37CF954B2D27}.png)

Компилирую шел-код при помощи [pwnasm](https://github.com/Nerx4s93/pwnasm).
![{69B4EFEF-FD5C-490C-9AAF-8765DF7A027A}](../../../../z.%20Images/{69B4EFEF-FD5C-490C-9AAF-8765DF7A027A}.png)

Эксплоит:
![{0DD3F7D5-F797-4164-B5F9-282DFF80755F}](../../../../z.%20Images/{0DD3F7D5-F797-4164-B5F9-282DFF80755F}.png)

Запуск:
![{926FF4D4-8F80-49B7-98A0-75A0CE5273FD}](../../../../z.%20Images/{926FF4D4-8F80-49B7-98A0-75A0CE5273FD}.png)

Ответ: `spbctf{i_c@n_do_this_even_without_binsh}`