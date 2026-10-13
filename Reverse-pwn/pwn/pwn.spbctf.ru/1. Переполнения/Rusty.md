[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn1_rusty)
Описание:
_rusty_ — это слабенькая малварь-криптор, которая шифрует исключительно флаги SPbCTF.
Предлагаю поиграться с Qira и посмотреть, как же он это делает.
P.S. Даже не пытайтесь применить навыки реверса, и уж тем более криптографии.
[rusty](https://pwn.spbctf.ru/files/overflow/rusty)  
[rusty_ct](https://pwn.spbctf.ru/files/overflow/rusty_ct)

Внутри rusty_ct лежит зашифрованный флаг:
```
kudo@WIN-2OOLFPOBK49:/mnt/c/virtual$ cat rusty_ct
Here is ct:
;���`t���rh�jn R��>��Q���
```

rusty должен как-то его расшифровать.
Запуск rusty:
```
kudo@WIN-2OOLFPOBK49:/mnt/c/virtual$ ./rusty
thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: Os { code: 2, kind: NotFound, message: "No such file or directory" }', src/main.rs:109:21
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```
Он не может найти какой-то файл.

Запуск под qira:
![](../../../../z.%20Images/{269FF759-7ADE-45D1-91EF-C4692AD91DD7}.png)
Тут конкретно видно, что 4099-ой командой он пытается открыть файл flag.txt.
Создаю файл `flag.txt` и заношу туда зашифрованный флаг.

Повторный запуск:
![](../../../../z.%20Images/{CD79DCB6-A618-462D-A8FA-6F3A76A63428}.png)

Ответ: `spbctf{plsnoreverseusedebuggers}`