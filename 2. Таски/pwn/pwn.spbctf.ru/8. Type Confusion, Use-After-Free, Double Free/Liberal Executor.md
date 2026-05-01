[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn8_mc3)
Описание:
Now the Command Executor may be freed!
`nc 109.233.56.90 11727`
**[mc3.elf](https://pwn.spbctf.ru/files/uaf/mc3/mc3.elf)**  
**[mc3.c](https://pwn.spbctf.ru/files/uaf/mc3/mc3.c)**

Исходный код:
``` C
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>

struct executor {
    char command[40];
};

struct namesayer {
    char firstName[16];
    char lastName[16];
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

void say_name(struct namesayer * obj) {
    printf(">>> I'm %s. %s %s.\n", obj->lastName, obj->firstName, obj->lastName);
}

struct namesayer * allocate_namesayer() {
    struct namesayer * obj = (struct namesayer *)malloc(sizeof(struct namesayer));
    printf("First name: ");
    scanf("%15s", obj->firstName);
    printf("Last name: ");
    scanf("%15s", obj->lastName);
    printf("Namesayer allocated!\n");
    
    say_name(obj);
    
    return obj;
}

void wedding(struct namesayer * obj) {
    char buf[256];
    
    printf("Husband's last name: ");
    scanf("%255s", buf);
    
    printf("You decided not to change your name.\n");
    
    say_name(obj);
}

int main() {
    setvbuf(stdin, NULL, _IONBF, 0);
    setvbuf(stdout, NULL, _IONBF, 0);
    
    struct executor * EXECUTOR = NULL;
    struct namesayer * NAMESAYER = NULL;
    
    char choice[4];
    
    while (1) {
        printf("\nMENU\n");
        printf("e - allocate executor\n");
        if (EXECUTOR) {
            printf("x - execute command\n");
            printf("f - free executor\n");
        }
        printf("n - allocate namesayer\n");
        if (NAMESAYER) {
            printf("s - say name\n");
            printf("w - marry someone\n");
        }
        printf("choice: ");
        alarm(120);
        if (scanf("%1s", choice) != 1) {
            return 0;
        }
        
        if (choice[0] == 'e') {
            EXECUTOR = allocate_executor();
        } else if (choice[0] == 'x' && EXECUTOR) {
            execute_command(EXECUTOR);
        } else if (choice[0] == 'f' && EXECUTOR) {
            free(EXECUTOR);
            printf("Executor freed!\n");
        } else if (choice[0] == 'n') {
            NAMESAYER = allocate_namesayer();
        } else if (choice[0] == 's' && NAMESAYER) {
            say_name(NAMESAYER);
        } else if (choice[0] == 'w' && NAMESAYER) {
            wedding(NAMESAYER);
        }
    }
}
```
После free(EXECUTOR) сама переменная не обнуляется, а значит команды `e` и `f` ещё доступны.

План атаки:
1. Аллоцировать `EXECUTOR`.
2. `free(EXECUTOR)`.
3. Аллоцировать `NAMESAYER` с именем `sh`.
4. Выполнить `x`.

Проверка:
![{6CA77F66-18E5-44EC-93FA-3202467E69E5}](../../../../z.%20Images/{6CA77F66-18E5-44EC-93FA-3202467E69E5}.png)

Ответ: `spbctf{844ca824ca00527024b2e7f901a2131c}`