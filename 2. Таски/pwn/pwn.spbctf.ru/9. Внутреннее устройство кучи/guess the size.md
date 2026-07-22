[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn9_mc1)
Описание:
Guess what is missed?
`nc 109.233.56.90 11800`

Таск содержит 42 задачи, по условию есть размер необходимой памяти, который передан в malloc, надо написать размер чанка.
![](../../../../z.%20Images/{F0B284E1-52E7-479D-A0ED-D5E02F572EE7}.png)

Солвер:
``` python
io = start()

for i in range(42):
    print(f'{i+1}/42')
    io.recvuntil(b'malloc(')
    inp = int(io.recvuntil(b')')[:-1])
    
    ans = (inp + 8 + 15) // 16 * 16 + 1
    io.send(p64(ans))

io.interactive()
```
Скрипт читает размер выделяемой памяти, добавляет размер заголовка и округляет в большую сторону, а затем добавляет 1 (prev in use).

Запуск:
![](../../../../z.%20Images/{8E3D6783-A4F2-45A2-966D-7A6D341A87C2}.png)

Ответ: `spbctf{a5befrrrrrrrrrrrrrr998d1a9584da3b}`