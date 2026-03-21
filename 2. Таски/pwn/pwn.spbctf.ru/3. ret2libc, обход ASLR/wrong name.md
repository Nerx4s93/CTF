[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_wrong_name)
Описание:
Угадай имя или, если ты ленивый, сделай что-нибудь другое...
`nc 109.233.56.90 11632`
**[wrong_name.elf](https://pwn.spbctf.ru/files/aslr/wrong_name/wrong_name.elf)**

Необходимо переполнить буфер `user_input` и перезаписать указатель на нужный мне.
![Pasted image 20260321184029](../../../../z.%20Images/Pasted%20image%2020260321184029.png)

Генерация имён:
![{1DBD4F77-5EEF-47FA-A7B0-96409C4BF842}](../../../../z.%20Images/{1DBD4F77-5EEF-47FA-A7B0-96409C4BF842}.png)
![{A16B448F-45FF-403C-A08A-3ECD76FCA261}](../../../../z.%20Images/{A16B448F-45FF-403C-A08A-3ECD76FCA261}.png)

Например, возьму Jerry:
![{751CF919-3D0B-4BDD-98A7-983DFBFDB4F0}](../../../../z.%20Images/{751CF919-3D0B-4BDD-98A7-983DFBFDB4F0}.png)

Солвер:
![{226F3B36-2FB0-4BC9-9AC9-AD35BF26E54A}](../../../../z.%20Images/{226F3B36-2FB0-4BC9-9AC9-AD35BF26E54A}.png)

Запуск:
![{EBAB9B0C-A684-46D8-BF75-F9A3BE020E23}](../../../../z.%20Images/{EBAB9B0C-A684-46D8-BF75-F9A3BE020E23}.png)

Ответ: `spbctf{ca2035ab440f221f8e94bf5461655f13}`