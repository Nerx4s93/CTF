[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn5_AR1)
Описание:
Я спрятал секрет, но у тебя есть шанс найти его самостоятельно. Флаг в формате spbctf{0xчисло_в_хексах}
`nc 109.233.56.90 11681`
**[ar1.elf](https://pwn.spbctf.ru/files/www/AR1/ar1.elf)**

Флаг лежит по адресу 0x404040, также в солвере необходимо учесть, что байты в памяти лежат в обратном порядке.
![{ECD70ADB-CD87-4CE5-B627-13DA44AF86DD}](../../../../z.%20Images/{ECD70ADB-CD87-4CE5-B627-13DA44AF86DD}.png)

Солвер:
![{6BDA00DD-AAAC-450A-A64C-1A81A727F810}](../../../../z.%20Images/{6BDA00DD-AAAC-450A-A64C-1A81A727F810}.png)

Запуск:
![Pasted image 20260408100141](../../../../z.%20Images/Pasted%20image%2020260408100141.png)

Ответ: `spbctf{0x2176331165239512}`