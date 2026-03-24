[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_mib)
Описание:
Внимание в бинарике чтение входных данных ошибочно делается из файлового дескриптора stdout - 1, поэтому если запускать бинарь из pwntools, то прога\ зависнет, но если запускать прогу из консоли, либо повесить прогу на socat или qira -s то все будет хорошо
`nc 109.233.56.90 11624`
**[mib](https://pwn.spbctf.ru/files/aslr/mib/mib)**  
**[libc.so.6](https://pwn.spbctf.ru/files/aslr/mib/libc.so.6)**

Программа печатает в stdout флаг, а затем вызывает функцию по введённому адресу `buf` с параметром `stdout`. 
![{76EFA9C3-C08D-4A9A-86E7-D52342BD7D90}](../../../../z.%20Images/{76EFA9C3-C08D-4A9A-86E7-D52342BD7D90}.png)

Необходимо считать адрес `system`, вычислить `libc_base`, а затем записать в buf адрес функции `puts`, чтобы считать с `stdout` флаг.
Оффсеты функций:
![{90F3F17E-2C93-44CE-8DD9-877679A630DD}](../../../../z.%20Images/{90F3F17E-2C93-44CE-8DD9-877679A630DD}.png)
![{F7FC869F-F4F2-4C7B-AEBC-D9F8F6BEF220}](../../../../z.%20Images/{F7FC869F-F4F2-4C7B-AEBC-D9F8F6BEF220}.png)

Солвер:
![{E9D7BF5B-39FD-4010-9411-BCE0CCD549EA}](../../../../z.%20Images/{E9D7BF5B-39FD-4010-9411-BCE0CCD549EA}.png)

Запуск:
![{CDBAED9A-46B2-4503-A469-9B67CF60AFB1}](../../../../z.%20Images/{CDBAED9A-46B2-4503-A469-9B67CF60AFB1}.png)

Ответ: `spbctf{5dbdc152ef4be37efde7066f45705c55}`