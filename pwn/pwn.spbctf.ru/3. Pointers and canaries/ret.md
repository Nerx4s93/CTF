[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc4)
Описание:
Перетрите адрес возврата
`nc 109.233.56.90 11605`
**[ret](https://pwn.spbctf.ru/files/canary/ret)**

Необходимо переполнить буфер + дополнительно 8 байт, а затем перетереть адрес возврата.
![{8FB9A43D-D5E7-4257-BB0E-CF6E900D0E71}](../../../z.%20Images/{8FB9A43D-D5E7-4257-BB0E-CF6E900D0E71}.png)

Солвер:
![{288975A0-1C2C-41D5-ADE1-138BC181F756}](../../../z.%20Images/{288975A0-1C2C-41D5-ADE1-138BC181F756}.png)

Запуск:
![{B19EB145-28FD-441E-B30E-FF42D4341DFB}](../../../z.%20Images/{B19EB145-28FD-441E-B30E-FF42D4341DFB}.png)

Ответ: `spbctf{7f6eba140c378e636419e0259268e546}`