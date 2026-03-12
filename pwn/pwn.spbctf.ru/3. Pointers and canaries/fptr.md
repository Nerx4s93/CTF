[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc1)
Описание:
Найдите указатель на строчку и перетрите его
`nc 109.233.56.90 11601`
**[fptr](https://pwn.spbctf.ru/files/canary/fptr)**

Необходимо переполнить буфер buf и записать в function_pointer указатель на функцию win.
![{C93BA7F6-02E6-45F8-976A-128A3B0F18F5}](../../../z.%20Images/{C93BA7F6-02E6-45F8-976A-128A3B0F18F5}.png)

Солвер:
![{1D9962EA-4A83-4435-B621-A8971BB00867}](../../../z.%20Images/{1D9962EA-4A83-4435-B621-A8971BB00867}.png)

Запуск:
![{586A35D3-70B9-4D18-8624-C1AF1A577AF2}](../../../z.%20Images/{586A35D3-70B9-4D18-8624-C1AF1A577AF2}.png)

Ответ: `spbctf{9c4ef124c0624b2ddbe6eb58f8be045a}`