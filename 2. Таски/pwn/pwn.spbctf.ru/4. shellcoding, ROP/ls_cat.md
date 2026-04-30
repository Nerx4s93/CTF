[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn4_ls_cat)
Описание:
(не)Просто. Прочитай. /(не)flag.
`nc 109.233.56.90 11665`
**[ls_cat.elf](https://pwn.spbctf.ru/files/shellcoding/ls_cat/ls_cat.elf)**

Разрешённые права:
![Pasted image 20260401125455](../../../../z.%20Images/Pasted%20image%2020260401125455.png)

Для начало надо считать содержимое каталога, чтобы найти файл с флагом.
Шел-код:
![{5FF2AAB8-9509-462F-BA7D-7A1B24FCE0F7}](../../../../z.%20Images/{5FF2AAB8-9509-462F-BA7D-7A1B24FCE0F7}.png)

Эксплоит:
![{BDD1A5D7-86FF-4F9D-B3F3-678E690521FF}](../../../../z.%20Images/{BDD1A5D7-86FF-4F9D-B3F3-678E690521FF}.png)

Вывод:
![{10B1AEE6-FBDD-4347-82A6-F97ECB91C967}](../../../../z.%20Images/{10B1AEE6-FBDD-4347-82A6-F97ECB91C967}.png)
Имеется файл с именем `s3cr3t_f1l3_w1th_fl0g`, его и надо прочитать.

Чтение файла:
![{ECCF98AC-37BA-4312-AE04-7F2157592922}](../../../../z.%20Images/{ECCF98AC-37BA-4312-AE04-7F2157592922}.png)![{49C40635-30F9-4DA5-B6EB-C8B1093E2FA6}](../../../../z.%20Images/{49C40635-30F9-4DA5-B6EB-C8B1093E2FA6}.png)

Запуск:
![{225D57E9-FE64-4674-AE7F-2349DB04EACF}](../../../../z.%20Images/{225D57E9-FE64-4674-AE7F-2349DB04EACF}.png)

Ответ: `spbctf{8378ef99d4ec9d3e5080907157476840}`
