[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_popa_ot_ropa)
В программе не хватает функционала. А добавьте свой в виде RCE!
`nc 109.233.56.90 11673`
**[popa_ot_ropa.elf](https://pwn.spbctf.ru/files/aslr/popa_ot_ropa/popa_ot_ropa.elf)**  
**[libc-2.31.so](https://pwn.spbctf.ru/files/aslr/popa_ot_ropa/libc-2.31.so)**

Имеется уязвимость переполнения буфера, можно создать ROP-цепочку.
![{AC157946-B7E2-4651-B6DE-7E7F56E0F897}](../../../../z.%20Images/{AC157946-B7E2-4651-B6DE-7E7F56E0F897}.png)
Причём ASLR отключён и программа предоставляет все адреса памяти.

Также имеется гаджет для `pop rdi`.
![Pasted image 20260325095208](../../../../z.%20Images/Pasted%20image%2020260325095208.png)

Заполнение буфера:
1. Буфер
2. `pop rdi` addr
3. `bin/sh` addr
4. `system` addr
К задаче прикреплена версия `libc`, там можно найти строку `bin/sh`, а адрес функции `system` находится внутри самой программы.

Солвер:
![{6DD2BC32-64D2-474E-9D49-31C886F931FF}](../../../../z.%20Images/{6DD2BC32-64D2-474E-9D49-31C886F931FF}.png)

Запуск:
![{CF611CBA-8C34-46CB-8BB3-FB7F68F82CAE}](../../../../z.%20Images/{CF611CBA-8C34-46CB-8BB3-FB7F68F82CAE}.png)

Ответ: `spbctf{370_3ch0_n3_p0p4_v07_5k0r0_bud37_zh0p4}`