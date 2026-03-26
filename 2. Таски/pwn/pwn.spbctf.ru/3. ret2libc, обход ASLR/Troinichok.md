[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_troinichok)
Описание:
Почти победа. Опять тренимся перетирать адрес возврата!
`nc 109.233.56.90 11623`
**[troinichok](https://pwn.spbctf.ru/files/aslr/troinichok/troinichok)**

Программа имеет уязвимость переполнения буфера, можно сделать переход на функцию `almost_win` и получить флаг.
![{6C508AB2-5F7B-4F10-A3ED-A2833B036F81}](../../../../z.%20Images/{6C508AB2-5F7B-4F10-A3ED-A2833B036F81}.png)
![{B4135DD9-A3FC-4660-B450-580E0A773EA5}](../../../../z.%20Images/{B4135DD9-A3FC-4660-B450-580E0A773EA5}.png)

Переход стоит осуществлять на данные сразу после `if`, чтобы получить флаг и не разбираться в логике работы `inc_leet`.
![{56F55E68-6C24-481C-AB71-1A2B12CE86CF}](../../../../z.%20Images/{56F55E68-6C24-481C-AB71-1A2B12CE86CF}.png)

Стоит отметить, что перезаписывать `rbp` на `AAAAAAAA` не получится, т.к. программа сломается, поэтому можно перезаписать `rbp` на случайную `rw` секцию [Memory](../../../../Reverse-pwn/Memory.md)
![{CFF9D2DF-C22C-4CB5-9E5E-8FCED95FE662}](../../../../z.%20Images/{CFF9D2DF-C22C-4CB5-9E5E-8FCED95FE662}.png)

Солвер:
![{E04D91B5-31F3-422C-8A5C-10CCCFB3EA50}](../../../../z.%20Images/{E04D91B5-31F3-422C-8A5C-10CCCFB3EA50}.png)

Запуск:
![{5867D17B-4E2D-448D-98FF-7F3BBF27572D}](../../../../z.%20Images/{5867D17B-4E2D-448D-98FF-7F3BBF27572D}.png)

Ответ: `spbctf{38102beb39eef92b972454827acb4939}`