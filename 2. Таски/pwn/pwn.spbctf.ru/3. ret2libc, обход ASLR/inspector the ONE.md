[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_inspector)
Описание:
Инспектор, какой вы по счету? Спереди или с конца?
`nc 109.233.56.90 11672`
**[inspector.elf](https://pwn.spbctf.ru/files/aslr/inspector_number_one/inspector.elf)**  
**[libc-2.31.so](https://pwn.spbctf.ru/files/aslr/inspector_number_one/libc-2.31.so)**

Вероятнее всего необходимо использовать one gadget.
![{02AB6468-EEAD-46F1-8E19-FCF5D8C5D510}](../../../../z.%20Images/{02AB6468-EEAD-46F1-8E19-FCF5D8C5D510}.png)

Найденные гаджеты:
![{0AC7718A-CD7F-4E92-955E-1B716DAA00DC}](../../../../z.%20Images/{0AC7718A-CD7F-4E92-955E-1B716DAA00DC}.png)

Солвер:
![{EA018F2A-FD30-48BE-A758-BBB6D9BCC266}](../../../../z.%20Images/{EA018F2A-FD30-48BE-A758-BBB6D9BCC266}.png)

Запуск:
![{1939C7F9-F77F-46C0-85FC-3B3B28076382}](../../../../z.%20Images/{1939C7F9-F77F-46C0-85FC-3B3B28076382}.png)
Первый же гаджет сработал.

Ответ: `spbctf{r3m3mb3r_1n5p3c70r_64d637_w17h_numb3r_0n3}`