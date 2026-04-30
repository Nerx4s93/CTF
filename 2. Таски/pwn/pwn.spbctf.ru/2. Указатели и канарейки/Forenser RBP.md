[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_forenser_rbp)
Описание:
Hi! Take this file: [**pid-4018-7ffc1efb8000-7ffc1efd9000**](https://pwn.spbctf.ru/files/canary/pid-4018-7ffc1efb8000-7ffc1efd9000)
It's a dump of a process's stack.
What's the **saved RBP value** of the function that will return to main? It will be the last saved RBP that points inside stack

Данное задание - продолжение от задачи [Forenser Retaddr](Forenser%20Retaddr.md).
Ответ на задачу - сохранённый rbp первой функции, вызванной в main.
![{C80202E1-ED51-4A7E-AA5F-66CD00A9AC44}](../../../../z.%20Images/{C80202E1-ED51-4A7E-AA5F-66CD00A9AC44}.png)

Ответ: `7F FC 1E FD 66 00`