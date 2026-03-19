[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_simple_surprise_2)
Описание:
Get the flag
`nc 109.233.56.90 11612`
**[simple_surprise_2](https://pwn.spbctf.ru/files/canary/simple_suprprise_2_fixed)**

В точке входа генерируется случайное значение канарейки и выводится в терминал:
![{24227DF6-F2E0-4590-9EA9-F6DF4084D9E5}](../../../../z.%20Images/{24227DF6-F2E0-4590-9EA9-F6DF4084D9E5}.png)

В функции `func_A` происходит считывание логина с консоли. Необходимо тут переполнить буфер, перезаписать канарейку и указать адрес возврата на функцию `print_flag`.
![{D8254D68-1216-4679-821C-13DC6A7E8B67}](../../../../z.%20Images/{D8254D68-1216-4679-821C-13DC6A7E8B67}.png)

Солвер:
![{D649EEEF-462C-4561-BB75-249806BA3799}](../../../../z.%20Images/{D649EEEF-462C-4561-BB75-249806BA3799}.png)

Ответ: `spbctf{B1N4RY_2_DZ_HA4RD}`