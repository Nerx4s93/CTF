[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_mc5)
Описание: Переполните, чтобы получить флаг.
nc 109.233.56.90 11585
Прикреплён: mc5_censored (elf file).

В данном бинаре происходит заполнение массива.
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  int count; // [rsp+18h] [rbp-28h] BYREF
  int i; // [rsp+1Ch] [rbp-24h]
  int storage[5]; // [rsp+20h] [rbp-20h] BYREF
  char giveflag[5]; // [rsp+37h] [rbp-9h] BYREF
  int v8; // [rsp+3Ch] [rbp-4h]

  v8 = 0;
  strcpy(giveflag, "NOPE");
  printf("How many numbers do you want to store (max. 5) ? ");
  scanf("%d", &count);
  for ( i = 0; i < count; ++i )
  {
    printf("Enter number # %d: ", i);
    scanf("%d", &storage[i]);
  }

  printf("Here are your numbers, well-kept: ");
  for ( i = 0; i < count; ++i )
  {
    if ( i > 0 )
      printf(", ");
    printf("%d", storage[i]);
  }

  printf("\n\n");
  printf("Now to the flag... Do you want flag - '%s'\n", giveflag);
  if ( !strcmp(giveflag, "YEAH") )
    printf("Flag is: spbctf{********************************}\n");
  else
    printf("Good, i won't give you flag.\n");
  return v8;
}
```

Необходимо заполнить буфер 5 символов, затем ввести распакованную строку 'xxxY', а затем строку 'EAH\0'.
``` stack
-0000000000000020         int storage[5];
-000000000000000C         // padding byte
-000000000000000B         // padding byte
-000000000000000A         // padding byte
-0000000000000009         char giveflag[5];
```


![{06D729FE-EA2B-4A1D-8E57-94ABB3B1E725}](../../../../z.%20Images/{06D729FE-EA2B-4A1D-8E57-94ABB3B1E725}.png)
![Pasted image 20260309115451](../../../../z.%20Images/Pasted%20image%2020260309115451.png)

Ответ: `spbctf{0mgy3s_that_s_t3h_r34l_1337spe4k}`