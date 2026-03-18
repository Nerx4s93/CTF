[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc2)
Описание:
Нужно изменить значение is_admin, записав туда не нулевое значение через перееханный указатель
`nc 109.233.56.90 11603`
**[echo_counter](https://pwn.spbctf.ru/files/canary/echo_counter)**

Чтобы получить флаг значение глобальной переменной `is_admin` должно иметь ненулевое значение.
![Pasted image 20260316224950](../../../z.%20Images/Pasted%20image%2020260316224950.png)

В функции play необходимо переполнить буфер, переписав указатель beep на is_admin.
![{5F81C476-12D6-4A9E-A48A-55B37D30578D}](../../../z.%20Images/{5F81C476-12D6-4A9E-A48A-55B37D30578D}.png)

Солвер:
![{F2CD4A18-E333-486A-969F-5BDF1A808CC8}](../../../z.%20Images/{F2CD4A18-E333-486A-969F-5BDF1A808CC8}.png)

Запуск:
![{43AA994A-B4F9-4F7D-9FBD-F767BA83DFDA}](../../../z.%20Images/{43AA994A-B4F9-4F7D-9FBD-F767BA83DFDA}.png)

Ответ: `spbctf{8d37ab5e5c104fec5d1671c4b67cb295}`