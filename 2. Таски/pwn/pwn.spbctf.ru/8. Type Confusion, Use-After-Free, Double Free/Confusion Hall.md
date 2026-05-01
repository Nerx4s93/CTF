[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn8_mc1)
Описание:
Numbers and Strs. Completely interchangeable.
`nc 109.233.56.90 11725`
**[mc1.elf](https://pwn.spbctf.ru/files/uaf/mc1/mc1.elf)**  
**[mc1.c](https://pwn.spbctf.ru/files/uaf/mc1/mc1.c)**

Исходный код:
``` C
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
#include <string.h>

struct number {
    unsigned long long num;
};

struct str {
    char * data;
};

void * OBJECTS[10];
int COUNT = 0;

char FLAG[64];

void add_number() {
    unsigned long long i;
    
    if (COUNT >= 10) {
        printf("Too many elements already\n");
        return;
    }
    
    printf("Enter number to add: ");
    scanf("%lld", &i);
    struct number * newObj = malloc(sizeof(*newObj));
    newObj->num = i;
    OBJECTS[COUNT++] = newObj;
    printf("Added!\n");
}

void add_str() {
    char buf[256];
    
    if (COUNT >= 10) {
        printf("Too many elements already\n");
        return;
    }
    
    printf("Enter str to add: ");
    scanf("%255s", buf);
    struct str * newObj = malloc(sizeof(*newObj));
    newObj->data = strdup(buf);
    OBJECTS[COUNT++] = newObj;
    printf("Added!\n");
}

void show_number() {
    int i;
    
    printf("Enter index to show: ");
    scanf("%d", &i);
    
    if (i < 0 || i >= COUNT) {
        printf("Wrong index\n");
        return;
    }
    
    struct number * obj = OBJECTS[i];
    
    printf("Number at [%d]: %lld\n", i, obj->num);
}

void show_str() {
    int i;
    
    printf("Enter index to show: ");
    scanf("%d", &i);
    
    if (i < 0 || i >= COUNT) {
        printf("Wrong index\n");
        return;
    }
    
    struct str * obj = OBJECTS[i];
    
    printf("Str at [%d]: '%s'\n", i, obj->data);
}

void edit_number() {
    int i;
    
    printf("Enter index to edit: ");
    scanf("%d", &i);
    
    if (i < 0 || i >= COUNT) {
        printf("Wrong index\n");
        return;
    }

    struct number * obj = OBJECTS[i];
    
    char buf[256];
    printf("Enter your instructions to modify the number: ");
    scanf("%255s", buf);
    
    for (int j = 0; j < strlen(buf); j++) {
        if (buf[j] == '+') {
            obj->num += 1;
        } else if (buf[j] == '-') {
            obj->num -= 1;
        } else if (buf[j] == '*') {
            obj->num *= 2;
        } else if (buf[j] == '/') {
            obj->num /= 2;
        }
    }
    
    printf("Now number at [%d]: %lld\n", i, obj->num);
}

void edit_str() {
    int i;
    
    printf("Enter index to edit: ");
    scanf("%d", &i);
    
    if (i < 0 || i >= COUNT) {
        printf("Wrong index\n");
        return;
    }

    struct str * obj = OBJECTS[i];
    
    char buf[256];
    printf("Enter new str: ");
    scanf("%255s", obj->data);
    
    printf("Now str at [%d]: '%s'\n", i, obj->data);
}

int main() {
    setvbuf(stdin, NULL, _IONBF, 0);
    setvbuf(stdout, NULL, _IONBF, 0);
    
    printf("Welcome to Confusion Hall!\n");
    
    FILE * f = fopen("flag.txt", "rb");
    if (! f) {
        printf("Couldn't open flag.txt\n");
        return 1;
    }
    fgets(FLAG, sizeof(FLAG), f);
    fclose(f);
    
    char choice[4];
    
    while (1) {
        printf("\nMENU:\n");
        printf("  (current count: %d)\n", COUNT);
        printf("i - add number\n");
        printf("s - add str\n");
        printf("n - show number\n");
        printf("t - show str\n");
        printf("I - modify number\n");
        printf("S - modify str\n");
        printf("choice: ");
        alarm(120);
        if (scanf("%1s", choice) != 1) {
            return 0;
        }
        
        if (choice[0] == 'i') {
            add_number();
        } else if (choice[0] == 's') {
            add_str();
        } else if (choice[0] == 'n') {
            show_number();
        } else if (choice[0] == 't') {
            show_str();
        } else if (choice[0] == 'I') {
            edit_number();
        } else if (choice[0] == 'S') {
            edit_str();
        }
    }
}
```

План атаки:
1. Создать число с адресом строки `FLAG`.
2. Вывести это число как строку.

Адрес флага:
![{E2D6A6FE-48FD-442B-8707-7E649E399C19}](../../../../z.%20Images/{E2D6A6FE-48FD-442B-8707-7E649E399C19}.png)
0x602120 = 6299936

Проверка:
![{30E808CC-8C75-4A63-A2C2-77F6B843C9E6}](../../../../z.%20Images/{30E808CC-8C75-4A63-A2C2-77F6B843C9E6}.png)

Ответ: `spbctf{a5bef727922b9ef4e7998d1a9584da3b}`