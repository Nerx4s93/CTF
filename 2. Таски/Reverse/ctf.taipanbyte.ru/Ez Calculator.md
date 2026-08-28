[TaipanByte](https://ctf.taipanbyte.ru/category/4/tasks)
Описание:
Парень подал на резюме на стажировку. Это его проект в портфолио. Мда... flag{}
[ez_calk.tb](https://ctf.taipanbyte.ru/api/uploads/7b38ff17-9abc-4fa8-b30f-c2664db641d6/ez_calk.tb?token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3ODgwMTcxMjEsImlhdCI6MTc4NzkzMDcyMSwicHVycG9zZSI6ImxvZ2luIiwicm9sZSI6IiIsInVzZXJfaWQiOjc3MX0.MscCVTWxBrWmcBQ-WiFXP2RY8TV6Kpl4SVku4kpCPEo)

``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  int v4; // [rsp+2Ch] [rbp-14h] BYREF
  int v5; // [rsp+30h] [rbp-10h] BYREF
  int v6; // [rsp+34h] [rbp-Ch] BYREF
  const char *v7; // [rsp+38h] [rbp-8h]

  _main();
  printf_0("Enter two numbers:");
  scanf("%d %d", &v6, &v5);
  puts_0("Enter your choice:\n 1.Addition\n 2.Subtraction\n 3.multiplication");
  puts_0("4.Division\n 5.Square of a number");
  scanf("%d", &v4);
  switch ( v4 )
  {
    case 1:
      printf_0("The sum of %d and %d is %d ", v6, v5, v6 + v5);
      break;
    case 2:
    case 5:
      printf_0("The Subtraction of %d and %d is %d", v6, v5, v6 - v5);
      break;
    case 3:
      printf_0("The multiplication of %d and %d is %d", v6, v5, v5 * v6);
      break;
    case 4:
      printf_0("The division of %d and %d is %d", v6, v5, v6 - v5);
      break;
    default:
      printf_0("Invalid operation");
      break;
  }
  v7 = "flag{bfff_it_is_R0F1_tasK}";
  return 0;
}
```

Ответ: `flag{bfff_it_is_R0F1_tasK}`