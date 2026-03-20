[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_hello_goodbye)
Описание:
Привет или пока. Выбирать только тебе.
`nc 109.233.56.90 11670`
**[hello-goodbye.elf](https://pwn.spbctf.ru/files/aslr/hello_goodbye/hello-goodbye.elf)**  
**[libc-2.31.so](https://pwn.spbctf.ru/files/aslr/hello_goodbye/libc-2.31.so)**

# Решение через ret2libc
Т.к. к задаче прикреплена libc, то при помощи IDA можно найти в ней адрес функции system.
![{9F7E4F08-6215-44C0-B568-E2E17196AB76}](../../../../z.%20Images/{9F7E4F08-6215-44C0-B568-E2E17196AB76}.png)

Солвер:
![{479F0B43-D4BE-4E7F-BF42-78BCD0FB5380}](../../../../z.%20Images/{479F0B43-D4BE-4E7F-BF42-78BCD0FB5380}.png)

Запуск:
![{6B21A9E0-50EA-492B-B6A0-E95157468B46}](../../../../z.%20Images/{6B21A9E0-50EA-492B-B6A0-E95157468B46}.png)

# Альтернативное решение
Внутри программы имеется функция system.
![Pasted image 20260320123412](../../../../z.%20Images/Pasted%20image%2020260320123412.png)
![{EEAF55E0-9760-4AE8-8B29-7272236BC776}](../../../../z.%20Images/{EEAF55E0-9760-4AE8-8B29-7272236BC776}.png)

Внутри основной программы есть вызов функции `(v6)(buf)`, т.к. первый параметр передаётся через регистр `rdi`, можно записать в `buf` значение `bin/sh\x00`, и при помощи переполнения буфера переписать адрес функции v6 на часть кода с `call _system`.
![{F9365761-F7F5-4CD2-948D-5002C3C94E75}](../../../../z.%20Images/{F9365761-F7F5-4CD2-948D-5002C3C94E75}.png)

Солвер:
![{E059AFAD-01A7-4C32-A44E-0466F7F4799E}](../../../../z.%20Images/{E059AFAD-01A7-4C32-A44E-0466F7F4799E}.png)

Запуск:
![Pasted image 20260320123732](../../../../z.%20Images/Pasted%20image%2020260320123732.png)

Ответ: `spbctf{h3ll0_600dby3_0r_5y573m}`