[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn7_heap_indexes)
Описание:
Guess my secret.
`nc 109.233.56.90 11705`
**[heap_indexes.elf](https://pwn.spbctf.ru/files/heap/heap_indexes/heap_indexes.elf)**

Main:
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  char s[256]; // [rsp+0h] [rbp-130h] BYREF
  int index; // [rsp+100h] [rbp-30h] BYREF
  int count; // [rsp+104h] [rbp-2Ch] BYREF
  FILE *stream; // [rsp+108h] [rbp-28h]
  void *data; // [rsp+110h] [rbp-20h]
  void *secret; // [rsp+118h] [rbp-18h]
  _QWORD *storage; // [rsp+120h] [rbp-10h]
  int i; // [rsp+12Ch] [rbp-4h]

  printf("Enter the size of storage with your secrets (32 max): ");
  __isoc99_scanf("%d", &count);
  getchar();
  if ( count > 32 || count <= 0 )
  {
    puts("Nope.");
    exit(1);
  }


  storage = make_storage(count);
  for ( i = 0; i < count; ++i )
  {
    printf("Enter your secret [%d]: ", i);
    fgets(storage[i], 32, stdin);
  }


  secret = store_secret();
  printf("I kept my secret somewhere...\nEnter secret idx to compare with mine: ");
  __isoc99_scanf("%d", &index);
  getchar();
  data = storage[index];
  if ( data && !memcmp(data, secret, 0x32u) )
  {
    puts("YOU WIN!");
    stream = fopen("/flag.txt", "r");
    memset(s, 0, sizeof(s));
    fread(s, 0x100u, 1u, stream);
    fclose(stream);
    puts(s);
    return 1;
  }
  else
  {
    puts("Sorry, but not this time.");
    return 0;
  }
}
```
index можно указать абсолютно любой.

Можно заметить, что ссылка не секрет тоже сохраняется в памяти, благодаря чему можно при помощи Out-Of-Bounds сослаться на секрет и сравнение пройдёт.
``` C
__int64 store_secret()
{
  __int64 secret; // [rsp+0h] [rbp-20h] BYREF
  __int64 *p_secret; // [rsp+8h] [rbp-18h]
  _QWORD *secret_ptr; // [rsp+10h] [rbp-10h]
  void *s; // [rsp+18h] [rbp-8h]

  s = malloc(0x140u);
  memset(s, 255, 0x140u);

  secret_ptr = malloc(8u);
  secret = read_secret();
  p_secret = &secret;
  *secret_ptr = secret;
  return *p_secret;
}
```

Красным цветом выделен нулевой индекс массива, чтобы достать до ссылки на секрет необходимо указать индекс `(0x440-0x2a0) / 8 = 52`.
![{A0550963-C07F-4574-AA69-80D333F585EF}](../../../../z.%20Images/{A0550963-C07F-4574-AA69-80D333F585EF}.png)

Проверка:
![{E8F1270B-EEB4-4D7A-9504-0424195DFD82}](../../../../z.%20Images/{E8F1270B-EEB4-4D7A-9504-0424195DFD82}.png)

Ответ: `spbctf{h74p_1nd3x35_4r3_7h3_54m3_p41n}`