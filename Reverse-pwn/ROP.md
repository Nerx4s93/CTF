ROP - Return Oriented Programming.

# Пример
Задача получить шел.

Допустим по адресу 0x13567 лежат инструкции:
``` nasm
pop rdi
ret
```

При помощи переполнения буфера мы прыгаем на 0x13567:
```
4   AAAAAAAA
5   AAAAAAAA
6   AAAAAAAA
7   old ebp | AAAAAAAA
8   ret | \x01\x35\x67
9   /bin/sh address
10  system address
```
После прыжка `rsp` указывает на `/bin/sh`, после выполнения `pop rdi` в регистр `rdi` кладётся адрес `/bin/sh`, `rsp` указывает на `system`, после выполнения `ret` производится переход в функцию `system` уже с подготовленным значением в регистре `rdi`. После вызова `system` получается доступ к шелу.

# ret2libc
Частный случай атаки ROP. 

**[one_gadget](https://github.com/david942j/one_gadget)** - утилита для поиска гаджетов получения шела. На вход принимает библиотеку, и выдаёт найденные гаджеты, которые дадут доступ к шел при определённых условиях (например, `rax` = 0 & `rdi` = 0).
[libc database search](https://libc.blukat.me/), [libc-database](https://libc.rip/) - базы данных libc, можно найти необходимую версию libc по окончанию адресов функций.
# ASLR
Address Space Layout Randomization - рандомизация памяти, механизм защиты.

Для локальных тестов:
Отключить рандомизацию памяти: `echo 0 > /proc/sys/kernel/randomize_va_space`
Включить рандомизацию памяти:   `echo 2 > /proc/sys/kernel/randomize_va_space`

Для обхода ASLR необходимо ликнуть функции.
Например, через уязвимость форматной строки вывести стек и получить адреса функций из libc, затем найти версию libc и libc base.