[FreeHackQuest](https://freehackquest.com/quest/51)
[reverse100](https://freehackquest.com/public/files/quests/110A06B0-1264-D83D-E9A9-B483048BD3A8_reverse100)
Описание:
You work for FBI and you need to hack this file from French bank..

``` C
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int v3; // ebx
  const char *v5; // eax
  int v6; // ebx
  int v7; // eax
  int v8; // eax
  _DWORD v9[3]; // [esp+1Ch] [ebp-Ch] BYREF

  v3 = 0;
  puts("\n-==== Just Relax with Debugger Challenge ====-");
  puts("-==== Before we begin You must have:");
  do
  {
    printf((&quiz)[v3]);
    printf(question);
    __isoc99_scanf("%d", v9);
    if ( v9[0] != 1 )
    {
      printf(bad);
      return 0;
    }
    v5 = (&like)[v3++];
    printf(v5);
  }
  while ( v3 != 4 );

  v6 = 24;
  puts("Calculating FLAG....");
  summ(49);
  printf("\nFLAG = ");
  do
  {
    v7 = flag[v6++];
    putchar(v7);
  }
  while ( v6 != 29 );
  LOBYTE(v6) = 0;
  do
  {
    v8 = flag[v6++];
    putchar(v8);
  }
  while ( v6 != 24 );
  puts("cal");
  return puts("[Kev@] team love your CPU! :))");
}
```

Перепрыгиваю проверку через дебагер и получаю флаг.
![](../../../z.%20Images/{BF907600-1958-4E68-A40F-AF873EB7214B}.png)

Ответ: `be121740bf988b2225a313fa1f107cal`