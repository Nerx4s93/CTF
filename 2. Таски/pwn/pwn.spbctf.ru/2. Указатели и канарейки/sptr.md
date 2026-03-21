[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn2_mc0)
Описание:
Найдите указатель на строчку и перетрите его
`nc 109.233.56.90 11600`
**[sptr](https://pwn.spbctf.ru/files/canary/sptr)**

Надо просто перетереть ссылку с "NO WIN" (unk_402008) на "WIN"
![{21ADAF1C-FB98-4C78-A1AA-4824EAA29CEA}](../../../../z.%20Images/{21ADAF1C-FB98-4C78-A1AA-4824EAA29CEA}.png)

Солвер:
![{4A6B6B28-78D9-471E-89B0-3145B8D1D1F7}](../../../../z.%20Images/{4A6B6B28-78D9-471E-89B0-3145B8D1D1F7}.png)

Запуск:
![{7C0DB1AD-83C9-4B96-822C-816DA160E999}](../../../../z.%20Images/{7C0DB1AD-83C9-4B96-822C-816DA160E999}.png)

Ответ: `spbctf{b0e0b0cedc622b2396e0069cd5a5f568}`