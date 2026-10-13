[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn4_call_fcn_rop)
Описание:
Just call win_fcn and make it print flag for you. And there is no shellcode now!
`nc 109.233.56.90 11651`
**[task](https://pwn.spbctf.ru/files/shellcoding/call_fcn_rop/task)**
**[libc-2.31.so](https://pwn.spbctf.ru/files/shellcoding/call_fcn_rop/libc-2.31.so)**

Программа:
![{FDC06553-4683-4100-AC32-5C2FE243F9D7}](../../../../z.%20Images/{FDC06553-4683-4100-AC32-5C2FE243F9D7}.png)
![{20EB8220-2438-43B5-9B49-486B77B87C1E}](../../../../z.%20Images/{20EB8220-2438-43B5-9B49-486B77B87C1E}.png)

Т.к. предоставлены все адреса и версия libc, то остаётся найти гаджеты и построить ROP-цепочку. ROP должен создать участок памяти с правами RWX, а затем при помощи того же ROP вызвать read, записать шел-код и перейти туда.
Шел-код должен вызывать этот код:
![{960220DA-1F39-433A-B71C-FFF80645FA5C}](../../../../z.%20Images/{960220DA-1F39-433A-B71C-FFF80645FA5C}.png)

Шел-код:
![{8CEA52D8-4708-4790-9BAA-AED2D5881026}](../../../../z.%20Images/{8CEA52D8-4708-4790-9BAA-AED2D5881026}.png)

Эксплоит:
![{A1DD3CD3-9528-4CEA-A39C-E21CC7F523BE}](../../../../z.%20Images/{A1DD3CD3-9528-4CEA-A39C-E21CC7F523BE}.png)
Гаджета `pop r9` нигде не было, но был `xor r9, r9`, т.к. в r9 должен быть 0, то этот гаджет пойдёт.

Запуск:
![{91B1FA2B-EF6D-467E-9D33-82303B19382E}](../../../../z.%20Images/{91B1FA2B-EF6D-467E-9D33-82303B19382E}.png)

Ответ: `spbctf{3f4578621a6bba6f4be76dfe481cd85d357af5c1}`