[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn4_read_me_not)
Описание:
test
`nc 109.233.56.90 11613`
**[read_me_not](https://pwn.spbctf.ru/files/shellcoding/read_me_not/read_me_not)**

В шел-коде запрещено использовать `read`, `write`, `open`.
![{F11677AA-B1BD-4D65-9E01-14F982AE6388}](../../../../z.%20Images/{F11677AA-B1BD-4D65-9E01-14F982AE6388}.png)

Для чтения можно использовать `pread64`, а для вывода `sendfile`.

Шел-код:
![{E7A4F079-EEEC-4B1A-A266-3CDE56373D6F}](../../../../z.%20Images/{E7A4F079-EEEC-4B1A-A266-3CDE56373D6F}.png)

Эксплоит:
![{9C5348D3-8E0F-4341-B4C3-6EE86BA00497}](../../../../z.%20Images/{9C5348D3-8E0F-4341-B4C3-6EE86BA00497}.png)

Запуск:
![Pasted image 20260402233020](../../../../z.%20Images/Pasted%20image%2020260402233020.png)

Ответ: `spbctf{c46a39d485a999fc81d48e2a48b59108}`