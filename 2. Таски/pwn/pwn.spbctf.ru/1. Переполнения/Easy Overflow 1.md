[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_mc4)
Описание: Переполните, чтобы получить флаг.
nc 109.233.56.90 11584
Прикреплён: mc4_censored (elf file).

Надо переполнить word, чтобы giveflag был равен "Yesss!".
![{4624DC9B-7032-4EBA-9F25-7319FBF49E43}](../../../../z.%20Images/{4624DC9B-7032-4EBA-9F25-7319FBF49E43}.png)

Чтобы пройти проверку надо ввести 256 (буфер) + 8 (отступ) + 'Yesss!'.
![{25A693FC-D224-4F87-AA5B-0BD11225BC18}](../../../../z.%20Images/{25A693FC-D224-4F87-AA5B-0BD11225BC18}.png)

![{35AE4CB3-839F-4B2A-9EF9-071E39791F39}](../../../../z.%20Images/{35AE4CB3-839F-4B2A-9EF9-071E39791F39}.png)
![{6806A5D2-8B76-4F8C-A0D9-877C672E90CE}](../../../../z.%20Images/{6806A5D2-8B76-4F8C-A0D9-877C672E90CE}.png)

Ответ: `spbctf{babys_f1rst_0verfl0ww}`
