Описание:
Язык программирования: C
- linux: s6.out
- windows: s6.exe
Флаг - правильный серийный номер
[s6.exe](https://freehackquest.com/public/quests/4D2EA88F-AF20-6821-DC5E-A7DE41BC7467_22AB2686-11B6-2D62-1883-4187C77182FA)

``` C
int __cdecl Check(char *Str)
{
  char v2; // [esp+7h] [ebp-1h]

  v2 = 0;
  if ( strlen(Str) >= 7 )
  {
    v2 = Str[3] == 'w';
    if ( Str[6] == 'D' )
      ++v2;
    if ( Str[5] == '3' )
      ++v2;
    if ( Str[2] == 'F' )
      ++v2;
    if ( Str[1] == '2' )
      ++v2;
    if ( Str[4] == '1' )
      ++v2;
  }
  PrintOK(v2 == 6);
  return PrintError(v2 != 6);
}
```

Ответ: `_2Fw13D`