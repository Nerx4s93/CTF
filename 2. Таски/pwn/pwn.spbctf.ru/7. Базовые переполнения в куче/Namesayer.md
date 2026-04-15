[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn7_mc2)
Описание:
This program can tell you your name. And execute some commands!
`nc 109.233.56.90 11712`
**[mc2.elf](https://pwn.spbctf.ru/files/heap/mc2/mc2.elf)**

Код:
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  executor *EXECUTOR; // [rsp+0h] [rbp-20h]
  namesayer *NAMESAYER; // [rsp+8h] [rbp-18h]
  char choice[4]; // [rsp+14h] [rbp-Ch] BYREF
  unsigned __int64 v7; // [rsp+18h] [rbp-8h]

  v7 = __readfsqword(0x28u);
  setvbuf(stdin, nullptr, 2, 0);
  setvbuf(stdout, nullptr, 2, 0);
  NAMESAYER = nullptr;
  EXECUTOR = allocate_executor();
  while ( 1 )
  {
    puts("\nMENU");
    puts("e - allocate executor");
    if ( EXECUTOR )
      puts("x - execute command");
    puts("n - allocate namesayer");
    if ( NAMESAYER )
    {
      puts("s - say name");
      puts("w - marry someone");
    }

    printf("choice: ");
    alarm(0x78u);
    if ( __isoc99_scanf("%1s", choice) != 1 )
      break;
    if ( choice[0] == 'e' )
    {
      EXECUTOR = allocate_executor();
    }
    else if ( choice[0] == 'x' && EXECUTOR )
    {
      execute_command(EXECUTOR);
    }
    else if ( choice[0] == 'n' )
    {
      NAMESAYER = allocate_namesayer();
    }
    else if ( choice[0] == 's' && NAMESAYER )
    {
      say_name(NAMESAYER);
    }
    else if ( choice[0] == 'w' && NAMESAYER )
    {
      wedding(NAMESAYER);
    }
  }
  return 0;
}

executor *__cdecl allocate_executor()
{
  executor *obj; // [rsp+8h] [rbp-8h]

  obj = malloc(0x40u);
  strcpy(obj->command, "ls -la");
  puts("Executor allocated!");
  return obj;
}

void __cdecl execute_command(executor *obj)
{
  printf("Executing %s\n", obj->command);
  system(obj->command);
}

namesayer *__cdecl allocate_namesayer()
{
  namesayer *obj; // [rsp+8h] [rbp-8h]

  obj = malloc(0x20u);
  printf("First name: ");
  __isoc99_scanf("%15s", obj);
  printf("Last name: ");
  __isoc99_scanf("%15s", obj->lastName);
  puts("Namesayer allocated!");
  say_name(obj);
  return obj;
}

void __cdecl wedding(namesayer *obj)
{
  char buf[264]; // [rsp+10h] [rbp-110h] BYREF
  unsigned __int64 v2; // [rsp+118h] [rbp-8h]

  v2 = __readfsqword(0x28u);
  printf("Husband's last name: ");
  __isoc99_scanf("%255s", buf);
  strcpy(obj->lastName, buf);
  puts("Name changed!");
  say_name(obj);
}
```

В функции `wedding` есть возможность переполнить буфер, перезаписать `lastname`, перезаписать заголовок в 16 байт, а потом написать команду, которая выполнится на сервере, например, `/bin/sh`.

Ход действий:
- Создать namesayer
- Создать объект executor ПОСЛЕ namesayer
- Переполнить буфер lastname и перезаписать команду в executor

Выполнение:
![](../../../../z.%20Images/{2BC78956-974C-4F47-BFF8-C8FBD1A9A810}.png)

Ответ: `spbctf{87a008904db77712942d756b1c9a8a50}`