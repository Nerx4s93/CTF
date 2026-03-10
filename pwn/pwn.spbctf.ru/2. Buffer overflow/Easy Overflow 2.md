[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_mc5)
Описание: Переполните, чтобы получить флаг.
nc 109.233.56.90 11585
Прикреплён: mc5_censored (elf file).

В данном бинаре происходит заполнение массива.
![Pasted image 20260309113703](../../../z.%20Images/Pasted%20image%2020260309113703.png)

Необходимо заполнить буфер 5 символов, затем ввести распакованную строку 'xxxY', а затем строку 'EAH\0'.
![{5041A91F-C59E-45F0-AD3B-97C7F250C762}](../../../z.%20Images/{5041A91F-C59E-45F0-AD3B-97C7F250C762}.png)
![{06D729FE-EA2B-4A1D-8E57-94ABB3B1E725}](../../../z.%20Images/{06D729FE-EA2B-4A1D-8E57-94ABB3B1E725}.png)

![Pasted image 20260309115451](../../../z.%20Images/Pasted%20image%2020260309115451.png)

Ответ: `spbctf{0mgy3s_that_s_t3h_r34l_1337spe4k}`