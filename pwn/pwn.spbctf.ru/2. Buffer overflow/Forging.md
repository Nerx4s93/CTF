[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_forging)
Описание: Hi! This is your [binary](https://pwn.spbctf.ru/files/overflow/forging)  
nc 109.233.56.90 11578

Тут надо просто переполнить буфер и переписать intы.
![{9D597E1D-E08D-407E-A399-353C00EF73D3}](../../../y.%20Images/{9D597E1D-E08D-407E-A399-353C00EF73D3}.png)
![{B46F1588-4A5A-4C67-A517-81937DF06093}](../../../y.%20Images/{B46F1588-4A5A-4C67-A517-81937DF06093}.png)

Солвер:
![{50744FB4-EAAD-4BED-B51D-C61E87A0004E}](../../../y.%20Images/{50744FB4-EAAD-4BED-B51D-C61E87A0004E}.png)

Проверка:
![{717198F0-50A7-439A-A884-CB4AF80B17F5}](../../../y.%20Images/{717198F0-50A7-439A-A884-CB4AF80B17F5}.png)

Ответ: `spbctf{forging_zeroes_via_c_string_is_not_so_hard}`
