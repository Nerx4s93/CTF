[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_mc9)
Описание:
Найдите адрес инструкции, которая положила ЭТО в память
**[mc9](https://pwn.spbctf.ru/files/overflow/mc9)**

Функция имеет обфусцированную функцию, которая как-то взаимодействует с памятью.
``` C
int __fastcall main(int argc, const char **argv, const char **envp)
{
  obfuscated_prepare_memory();
  printf("So, instruction at what address did put this number into memory: %d ?\n", themem[69]);
  return 0;
}

void __cdecl obfuscated_prepare_memory()
{
  void *_3_obfuscated_prepare_memory_jumpTab[36]; // [rsp+10h] [rbp-240h] BYREF
  unsigned __int64 _3_obfuscated_prepare_memory_next; // [rsp+138h] [rbp-118h] BYREF

  memcpy(_3_obfuscated_prepare_memory_jumpTab, &off_401180, sizeof(_3_obfuscated_prepare_memory_jumpTab));
  6_obfuscated_prepare_memory_obfuscated_prepare_memory_split_3(&_3_obfuscated_prepare_memory_next);
  __asm { jmp     rax }
}
```

Необходимо посмотреть трейс программы по qira и посмотреть все её системные вызовы.
![](../../../../z.%20Images/{AD858563-AD72-4E15-81E6-690F712437F8}.png)![](../../../../z.%20Images/{1E9904FE-351C-46AA-BF98-79341EE5C0CD}.png)

Программа 28905 командой печатает вот это значение:
![](../../../../z.%20Images/{FD14B9FE-BBF2-426E-96BE-4A815F28E5DA}.png)

Необходимо узнать параметры вызова функции `printf`, чтобы узнать из какого адреса передаётся число `9395`.

Число `9395` (`0x24b3` в hex) лежит в памяти по адресу `0x602984`, взаимодействие с этим адресом происходило в обфусцированной функции ассемблерной командой, которая выполнилась `12145`-ой.
![](../../../../z.%20Images/{4AFE0260-97EE-40E1-B896-AA3DB303A209}.png)

Адрес команды: `0x400e88`.
![](../../../../z.%20Images/{A8211230-419D-48DD-A96B-01E1C6FAC9F1}.png)

Ответ: `0x400e88`