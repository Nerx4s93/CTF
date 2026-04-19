[FreeHackQuest](https://freehackquest.com/quest/49)
Описание:
You work for FBI and you need to hack this file from German bank...
[reverse200_exe](https://freehackquest.com/public/files/quests/5ABE29EE-F017-487A-C6F0-503C4759C13D_reverse200_exe)

``` C
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int v4; // eax
  int v5; // eax
  int v6; // eax
  int v7; // [esp+4h] [ebp-4h] BYREF

  v7 = 1;
  printf("-== You are welcome! ==-\n");
  printf("-> I think you don't dump your brain\n");
  printf("-> Before I take you flag give one number\n");
  scanf("%d", &v7);
  printf("-> Your flag = 6da81515a58c4291f695c0caec2976d0\n");

  if ( v7 / v7 == 1 )
  {
    printf("See you later ...............and bye\n");
    return 0;
  }
  else
  {
    printf("Stage 1 of 3 complete\n");
    if ( argc == 1 )
    {
      printf("Give me one argument!\n");
      return 0;
    }
    else
    {
      printf("Stage 2 of 3 complete\n");
      if ( argc == 3 )
      {
        printf("Stage 3 of 3 complete\n");
        printf("Wow! You win!:\n Flag = ");
        v4 = 10;
        v7 = 10;
        do
        {
          printf("%c", byte_40226C[v4]);
          v4 = v7 + 1;
          v7 = v4;
        }
        while ( v4 < 20 );
        v5 = 0;
        v7 = 0;
        do
        {
          printf("%c", byte_40226C[v5]);
          v5 = v7 + 1;
          v7 = v5;
        }
        while ( v5 < 10 );
        v6 = 20;
        v7 = 20;
        do
        {
          printf("%c", byte_40226C[v6]);
          v6 = v7 + 1;
          v7 = v6;
        }
        while ( v6 < 30 );
        printf("d0\n");
        return 0;
      }
      else
      {
        printf("Give me second argument\n");
        return 0;
      }
    }
  }
}
```

Патчу первое условие, чтобы программа смогла продвинуться дальше.
``` nasm
; jnz     short loc_40105D
jz     short loc_40105D
```

Далее по коду он просит абсолютно два любых аргумента.
![{0524AF00-8FD0-4CD9-BC60-77488554091A}](../../../z.%20Images/{0524AF00-8FD0-4CD9-BC60-77488554091A}.png)

Ответ: `8c4291f6956da81515a5c0caec2976d0 `