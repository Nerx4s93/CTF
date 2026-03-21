[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_secret)
Описание:
Форматные строки говоришь? Окай...
`nc 109.233.56.90 11630`
**[secret.elf](https://pwn.spbctf.ru/files/aslr/secret/secret.elf)**

Необходимо при помощи уязвимости форматной строки прочесть переменную `s`, а затем отправить её.
![{77B165C6-DDE5-4BDF-A228-D7FCFF4A7C04}](../../../../z.%20Images/{77B165C6-DDE5-4BDF-A228-D7FCFF4A7C04}.png)
Адрес `s`: 6 (регистры) + 4 (user_input) + 1 (format) = 11.
Получается, чтобы прочитать `s` со стека необходимо ввести `%11$s`.

Солвер:
![Pasted image 20260321182605](../../../../z.%20Images/Pasted%20image%2020260321182605.png)

Запуск:
![{05B3ACC8-84A8-401A-85BD-4C95E474AD0A}](../../../../z.%20Images/{05B3ACC8-84A8-401A-85BD-4C95E474AD0A}.png)

Ответ: `spbctf{a6a187fca1a645cae80e49390cff8c9d}`