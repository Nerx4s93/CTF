# Регистры
- EAX (Accumulator): для арифметических операций;
- ECX (Counter): для хранения счетчика цикла;
- EDX (Data): для арифметических операций и операций ввода-вывода;
- EBX (Base): указатель на данные ;
- ESP (Stack pointer): указатель на верхушку стек;
- EBP (Base pointer): указатель на базу стека внутри функции;
- ESI (Source index): указатель на источник при операциях с массивом;
- EDI (Destination index): указатель на место назначения в операциях с массивами;
- EIP: указатель адреса следующей инструкции для выполнения;
- EFLAGS: регистр флагов, содержит биты состояния процессора;
- R8 - R15: регистры общего назначения.

# Соглашения о вызовах
## gcc
### Linux
``` nasm
; big_function(1, 2, 3, 4, 5, 6, 7, 8 , 9, 10)

push 10
push 9
push 8
push 7
mov r9, 6          ; r9d 
mov r8, 5          ; r8d
mov rcx, 4         ; ecx
mov rdx, 3         ; edx
mov esi, 2         ; esi
mov rdi, 1         ; edi
call big_function
add rsp, 32
```
Первые 6 параметров через регистры, остальные через стек.

# syscall'ы
[Chromium OS Docs - Linux System Call Table](https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md)

``` nasm
; x64
mov rax, <function>
mov rdi, <arg0>
mov rsi, <agr1>
mov rdx, <arg2>
mov r10, <arg3>
mov r8, <arg4>
mov r9, <arg5>
syscall
```

``` nasm
; x86_64
mov rax, <function>
mov ebx, <arg0>
mov ecx, <agr1>
mov edx, <arg2>
mov esi, <arg3>
mov edi, <arg4>
mov ebp, <arg5>
int 0x80
```