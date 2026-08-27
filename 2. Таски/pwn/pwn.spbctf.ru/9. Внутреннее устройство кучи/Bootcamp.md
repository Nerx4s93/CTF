[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn9_mc6)
Описание:
Go to the heap chunk training bootcamp to get used to chunk sizes
`nc 109.233.56.90 11736`

Подключение:
![](../../../../z.%20Images/{884A625F-D2B5-4514-A6B0-F0A5BEB1DDA3}.png)
Задача требует написать максимальный размер с помощью которого можно было бы выделить данные чанк.

Солвер:
``` python
io = start()

for _ in range(15):
    io.recvuntil(b'***') # чтение до заголовка задания
    io.recvline()
    for i in range(8):
        io.recvuntil(b'__ ') # пустые байты
    
    # чтение размера
    num = 0
    p = 1
    for i in range(8):
        byte = int(io.recvuntil(b' ').decode()[:-1], 16)
        num += byte << (8 * i)
        p += 1
    num = num // 0x8 * 0x8 # удаление служебных битов
    
    max_size = num - 8 # chunk_size
    io.sendlineafter(b'? ', hex(max_size).encode())

io.interactive()
```

Запуск:
![](../../../../z.%20Images/{3E18BFE9-742C-4676-B71E-3EDFC69478DD}.png)

Ответ: `spbctf{fa05e31672fce83523625cd41f2eac35}`