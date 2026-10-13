[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_mc4)
Описание: Переполните, чтобы получить флаг.
nc 109.233.56.90 11584
Прикреплён: mc4_censored (elf file).

Надо переполнить word, чтобы giveflag был равен "Yesss!".
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  char word[256]; // [rsp+10h] [rbp-110h] BYREF
  char giveflag[4]; // [rsp+118h] [rbp-8h] BYREF
  int v6; // [rsp+11Ch] [rbp-4h]

  v6 = 0;
  strcpy(giveflag, "No.");
  printf("What's your favorite word? ");
  scanf("%s", word);
  if ( !strcmp(giveflag, "Yesss!") )
    printf("Flag is: spbctf{********************************}\n");
  else
    printf("Good, but i won't give you flag.\n");
  return v6;
}
```

Чтобы пройти проверку надо ввести 256 (буфер) + 8 (отступ) + 'Yesss!'.
``` stack
char word[256];
// padding byte
// padding byte
// padding byte
// padding byte
// padding byte
// padding byte
// padding byte
// padding byte
char giveflag[4];
```

![{35AE4CB3-839F-4B2A-9EF9-071E39791F39}](../../../../z.%20Images/{35AE4CB3-839F-4B2A-9EF9-071E39791F39}.png)
![{6806A5D2-8B76-4F8C-A0D9-877C672E90CE}](../../../../z.%20Images/{6806A5D2-8B76-4F8C-A0D9-877C672E90CE}.png)

Ответ: `spbctf{babys_f1rst_0verfl0ww}`
