[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_cmd)
Описание:
Надо сделать переполнение переменной username на стеке и получить шелл.  
[task](https://pwn.spbctf.ru/files/overflow/cmd/task)
nc 109.233.56.90 11577

Программа просит ввести имя, а затем вызывает команду.
``` C
printf("Enter your name: ");
read(0, buffer, 0x400u);
printf("Successful login at ");
system(command);

// stack:
// -0000000000000200         char buffer[256];
// -0000000000000100         char command[8];
```
Необходимо переполнить буфер и написать в конце `/bin/sh`, чтобы открыть шел.

![Pasted image 20260309150855](../../../../z.%20Images/Pasted%20image%2020260309150855.png)

Ответ: `spbctf{stack_buffer_shell_what}`