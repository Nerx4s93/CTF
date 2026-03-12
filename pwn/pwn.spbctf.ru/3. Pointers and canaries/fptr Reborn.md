[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc3)
Описание:
Найдите указатель на функцию и перетрите его
`nc 109.233.56.90 11602`
**[fptr2](https://pwn.spbctf.ru/files/canary/fptr2)**

Код:
![{6D3AAF2B-4DFF-4627-A1C9-8088C858A7AD}](../../../z.%20Images/{6D3AAF2B-4DFF-4627-A1C9-8088C858A7AD}.png)

Есть вот такая функция:
![{680E2BF8-FEBB-4B4A-9DBA-D70A5DE8A5A7}](../../../z.%20Images/{680E2BF8-FEBB-4B4A-9DBA-D70A5DE8A5A7}.png)
Её необходимо вызвать.

Надо переполнить буфер, в function_pointer указать ссылку на функцию my_system, в function_arg указать ссылку на память после самой function_arg, а в конце написать команду.
![{4D7B786A-CF57-4282-9C0F-E12961539516}](../../../z.%20Images/{4D7B786A-CF57-4282-9C0F-E12961539516}.png)

Солвер:
![{66347D7F-6AEA-41DA-B9C3-5CC79B776C6F}](../../../z.%20Images/{66347D7F-6AEA-41DA-B9C3-5CC79B776C6F}.png)

Запуск:
![Pasted image 20260312230435](../../../z.%20Images/Pasted%20image%2020260312230435.png)

Ответ: `spbctf{9fdf376b920f892d1d312985ae102e89}`