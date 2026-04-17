[FreeHackQuest](https://freehackquest.com/quest/46)
Описание:
You work for FBI and you need to hack this file from Belgian bank...
[reverse50_win32_exe](https://freehackquest.com/public/files/quests/507D1FB4-AFAF-D5FB-0369-8A269FFBDFE9_reverse50_win32_exe)

``` C
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char Str2[4]; // [esp+4h] [ebp-4h] BYREF

  strcpy(Str2, " ");
  printf("*********************\n");
  printf("IDS system v.0.1 (alpha)\n");
  printf("*********************\n");
  printf("Enter a valid key:\n");
  printf("key format: XXXX-XXXX (only chars sorry)\n");
  scanf("%s", Str2);
  if ( !strncmp("DEAD-BEEF", Str2, 11u) )
  {
    printf("You win!\n FLAG=");
    printf("755f85c2723bb39381c7379a604160d8\n");
  }
  else
  {
    printf("Maybe try this key : bae60998ffe4923b131e3d6e4c19993e\n");
  }
  return 0;
}
```

Ответ: `755f85c2723bb39381c7379a604160d8`