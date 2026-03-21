[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_cmd)
Описание:
Надо сделать переполнение переменной username на стеке и получить шелл.  
[task](https://pwn.spbctf.ru/files/overflow/cmd/task)
nc 109.233.56.90 11577

Программа просит ввести имя, а затем вызывает команду.
![{08758F06-8745-4B0E-BAB1-E41FD31C3EBE}](../../../../z.%20Images/{08758F06-8745-4B0E-BAB1-E41FD31C3EBE}.png)![{E67F0811-26D9-470F-9C7F-540477EC341B}](../../../../z.%20Images/{E67F0811-26D9-470F-9C7F-540477EC341B}.png)
Необходимо переполнить буфер и написать в конце `/bin/sh`, чтобы открыть шел.

![Pasted image 20260309150855](../../../../z.%20Images/Pasted%20image%2020260309150855.png)

Ответ: `spbctf{stack_buffer_shell_what}`