[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_mc8)
Описание:
Переполните автоматом, чтобы получить флаг. У вас одна секунда!
`nc 109.233.56.90 11588`
**[mc8_censored](https://pwn.spbctf.ru/files/overflow/mc8_censored)**
**[mc8_censored_notimeout](https://pwn.spbctf.ru/files/overflow/mc8_censored_notimeout)** — для тренировки локально без таймаута

![Pasted image 20260310155045](../../../y.%20Images/Pasted%20image%2020260310155045.png)
![{8AA37E39-EB0B-455D-979B-CECDE8BEBCCA}](../../../y.%20Images/{8AA37E39-EB0B-455D-979B-CECDE8BEBCCA}.png)
Программа печатает рандомное число, которое при помощи pwntools надо считать, а затем необходимо переполнить буфер userInput, чтобы переписать num1 на рандомное число из random.

Солвер:
![{401CF10B-58DB-412E-BF03-88E26065628C}](../../../y.%20Images/{401CF10B-58DB-412E-BF03-88E26065628C}.png)

Запуск:
![{E7893D1A-74C4-42F3-A9D5-97D404AAC5CF}](../../../y.%20Images/{E7893D1A-74C4-42F3-A9D5-97D404AAC5CF}.png)

Ответ: `spbctf{w3lp_that_w4s_34sy_aft3r_prev1ous_1}`