[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_admin)
Описание: Надо сделать переполнение переменной username в bss секции и стать админом.  
[task](https://pwn.spbctf.ru/files/overflow/admin/task
nc 109.233.56.90 11575

Необходимо просто ввести 257 символ, чтобы в is_admin лежало ненулевое значение.
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  char *v3; // rax

  is_admin = 0;
  printf("Enter your name: ");
  __isoc99_scanf("%s", username);
  if ( is_admin )
  {
    v3 = getenv("FLAG");
    printf("Welcome back, admin!\nHere is your flag: %s\n", v3);
  }
  else
  {
    printf("Welcome, %s!\n", username);
  }
  return 0;
}

// .bss:000000000040A0 username        db 100h dup(?)
// .bss:000000000040A0
// .bss:000000000041A0                 public is_admin
// .bss:000000000041A0 is_admin        db ?
```

Проверка:
![{98B9E773-E9AA-40C5-8562-ED9DF3935F30}](../../../../z.%20Images/{98B9E773-E9AA-40C5-8562-ED9DF3935F30}.png)

Ответ: `spbctf{this_is_simple_overflow}`