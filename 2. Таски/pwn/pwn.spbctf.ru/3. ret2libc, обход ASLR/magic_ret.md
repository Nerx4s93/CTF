[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn3_magic_ret)
Описание:
Всё в твоих руках, верь мне.
`nc 109.233.56.90 11633`
**[magic_ret.elf](https://pwn.spbctf.ru/files/aslr/magic_ret/magic_ret.elf)**

Программа печатает адрес libc. Необходимо найти версию данной libc на [libc database search](https://libc.blukat.me/) и передать значения адреса функции и строки `bin/sh` в терминал
![Pasted image 20260321190428](../../../../z.%20Images/Pasted%20image%2020260321190428.png)

Адрес libc:
![{0EC9A520-D534-4665-AEAD-A52360B22A5B}](../../../../z.%20Images/{0EC9A520-D534-4665-AEAD-A52360B22A5B}.png)

Нашлось несколько версий, необходимо перебрать все.
![{D5B26F49-BB4C-43B2-9546-96F0155912F2}](../../../../z.%20Images/{D5B26F49-BB4C-43B2-9546-96F0155912F2}.png)

Солвер:
![{6F4FD272-C4E3-420C-863D-F0C0D5506BB2}](../../../../z.%20Images/{6F4FD272-C4E3-420C-863D-F0C0D5506BB2}.png)

Запуск:
![{6B9AAB02-C23A-4C57-9A12-67D27857C321}](../../../../z.%20Images/{6B9AAB02-C23A-4C57-9A12-67D27857C321}.png)

Ответ: `spbctf{fd524db64260874db5302d7607694b08}`