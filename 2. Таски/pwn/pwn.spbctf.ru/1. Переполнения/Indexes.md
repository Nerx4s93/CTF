[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_indexes)
Описание: Hi! I created very secure [system](https://pwn.spbctf.ru/files/overflow/indexes) to store my secrets. Can you bypass it?
nc 109.233.56.90 11572

Чтобы получить флаг secretPtr должен быть равен s2.
``` C
if ( userInput <= 32 )
{
  for ( i = 0; i < (int)userInput; ++i )
  {
    puts("enter your secret: ");
    *(_QWORD *)&global[8 * i] = malloc(32u);
    fgets(*(char **)&global[8 * i], 32, stdin);
  }

  puts("Enter secret idx to check: ");
  __isoc99_scanf("%d", &secretIndex);
  getchar();
  
  secretPtr = *(void **)&global[8 * secretIndex];
  
  if ( secretPtr && !memcmp(secretPtr, s2, 0x32u) )
  {
    puts("YOU WIN!");
    stream = fopen("flag.txt", "r");
    memset(s, 0, 0x100u);
    fread(s, 0x100u, 1, stream);
    fclose(stream);
    puts(s);
    return 1;
  }
  else
  {
    puts("Nope...");
    return 0;
  }
}
```

Из массива global можно добраться до массива s2.
``` bss
.bss:00005555555602040 global      db 16640 dup(?)  ; Массив указателей
.bss:00005555555606140 s2          dq ?             ; Секретный указатель (цель)
```
Индекс = 16640 / 8 = 2080.

Проверка:
![{89E2D32D-00BC-42D1-948F-DA5DF68F165F}](../../../../z.%20Images/{89E2D32D-00BC-42D1-948F-DA5DF68F165F}.png)

Ответ: `spbctf{well_I_should_not_forget_to_check_indexes}`