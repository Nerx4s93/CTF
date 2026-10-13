[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_forenser_retaddr)
Описание:
Hi! Take this file: [**pid-4018-7ffc1efb8000-7ffc1efd9000**](https://pwn.spbctf.ru/files/canary/pid-4018-7ffc1efb8000-7ffc1efd9000)
It's a dump of a process's stack.
What's the **return address** of the function that will return last (i.e., `main`)?
_It might be strange and surprising_

Данное задание - продолжение от задачи [Forenser Canary](Forenser%20Canary.md).
В [Forenser Canary](Forenser%20Canary.md) я ориентировался на функцию libc_start_main, её мы уже нашли.
![{490AFC3D-65FE-4F3C-BF40-4B0872D7BC86}](../../../../z.%20Images/{490AFC3D-65FE-4F3C-BF40-4B0872D7BC86}.png)

Ответ: `7F 59 2C 8D 6C CA`