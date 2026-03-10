[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_mc7)
Описание:
Переполните автоматом, чтобы получить флаг. У вас одна секунда!
`nc 109.233.56.90 11587`
**[mc7_censored](https://pwn.spbctf.ru/files/overflow/mc7_censored)**
**[mc7_censored_notimeout](https://pwn.spbctf.ru/files/overflow/mc7_censored_notimeout)** — для тренировки локально без таймаута

![Pasted image 20260310153110](../../../z.%20Images/Pasted%20image%2020260310153110.png)
![{8D6D2898-CAA7-4150-A106-74707369EB5F}](../../../z.%20Images/{8D6D2898-CAA7-4150-A106-74707369EB5F}.png)
Программа печатает рандомное число, которое при помощи pwntools надо считать, а затем необходимо переполнить буфер userInput, чтобы переписать string1 на рандомное число из verify.

Солвер:
![Pasted image 20260310154552](../../../z.%20Images/Pasted%20image%2020260310154552.png)

Запуск:
![{8A365684-9972-4687-B9DC-02B025A659E2}](../../../z.%20Images/{8A365684-9972-4687-B9DC-02B025A659E2}.png)

Ответ: `spbctf{how_d1d_y0u_l1ke_the_g37s_func}`