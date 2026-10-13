[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_simple_surprise_1)
Описание:
Get the flag
`nc 109.233.56.90 11611`
**[simple_surprise_1](https://pwn.spbctf.ru/files/canary/simple_surprise_1)**

Необходимо переполнить буфер, чтобы число было неравно.
``` c
int work()
{
  char message[268]; // [rsp+0h] [rbp-110h] BYREF
  int v2; // [rsp+10Ch] [rbp-4h]

  v2 = -889275714;
  setvbuf(stdout, nullptr, 2, 0);
  puts("Welcome to task:");

  gets(message);
  if ( v2 == -889275714 )
    return puts("Good but not enough");
  puts("Hacker!");
  return print_flag();
}
```

Запуск:
![{104996C7-FD95-4B02-99F5-0174373442D0}](../../../../z.%20Images/{104996C7-FD95-4B02-99F5-0174373442D0}.png)

Ответ: `spbctf{B1N4RY_1_DZ}`