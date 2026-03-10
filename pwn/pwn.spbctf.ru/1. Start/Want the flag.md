[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_mc2_2)
Описание: Убедите бинарник, что ХОТИТЕ флаг
Прикреплён: mc2 (elf file).

Происходит сравнение глобальной беременной со строкой.
![{5ED12412-159C-49BA-99F6-0631EB560A64}](../../../z.%20Images/{5ED12412-159C-49BA-99F6-0631EB560A64}.png)

Глобальная переменная лежит по адресу 0x60B040.
![{1FB761B9-A976-442A-8D2F-08DA6D0F3D5B}](../../../z.%20Images/{1FB761B9-A976-442A-8D2F-08DA6D0F3D5B}.png)

Меняю значение в дебагере:
![Pasted image 20260308203912](../../../z.%20Images/Pasted%20image%2020260308203912.png)
![Pasted image 20260308204008](../../../z.%20Images/Pasted%20image%2020260308204008.png)

![{58694EBE-ACA5-4EA0-9FF0-14A8A06E4385}](../../../z.%20Images/{58694EBE-ACA5-4EA0-9FF0-14A8A06E4385}.png)

Ответ: `spbctf{ed1t1ng_t3h_memory_l1ke_a_PRO}`