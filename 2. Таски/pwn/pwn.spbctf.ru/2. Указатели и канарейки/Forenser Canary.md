[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_forenser_canary)
Описание:
Hi! Take this file: [**pid-4018-7ffc1efb8000-7ffc1efd9000**](https://pwn.spbctf.ru/files/canary/pid-4018-7ffc1efb8000-7ffc1efd9000)
It's a dump of a process's stack.
What's the **stack canary** in this process?

Ориентироваться стоит на функцию libc_start_main 
![]({370F4BF2-AEEB-4F74-94F7-E074C9DFFA71}.png)
Значение `00 87 4F F0 EA F2 46 7A` повторяется дважды, один ведёт в libc, второй в main => это и есть канарейка.

Ответ: `00 87 4F F0 EA F2 46 7A`