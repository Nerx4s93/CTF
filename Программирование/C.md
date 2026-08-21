
# Массивы
``` C
#include <stdio.h>
 
int main(void)
{
    int numbers[] = { 5, 6, 7};
    size_t size = sizeof(numbers);
    printf("numbers size: %zu \n", size);     // numbers size: 12
    return 0;
}
```