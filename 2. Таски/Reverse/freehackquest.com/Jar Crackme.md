[FreeHackQuest](https://freehackquest.com/quest/225)
[crackme.jar](https://freehackquest.com/public/quests/C7D48DD5-8E21-23C1-3CDE-B1D04E13B856_B5C6AD82-E895-DE2F-FCB2-4893EBE7F47A)

``` Java
package defpackage;
  
  
/* loaded from: crackme.jar:Main.class */
  
public class Main {
  
    public static void main(String[] strArr) {
  
        int[] iArr = {229, 102, 166, 229, 227, 205, 80, 224, 122, 168, 56, 185, 1, 154, 43, 127, 139, 83, 211, 202, 240};
  
        int[] iArr2 = {212, 7, 249, 175, 162, 155, 49, 191, 62, 247, 90, 214, 108, 248, 116, 62, 255, 60, 190, 163, 155};
  
        boolean z = true;
  
        if (strArr.length == 0) {
  
            System.out.println("Usage: ./crackme password");
  
            return;
  
        }
  
        if (strArr[0].length() != iArr.length) {
  
            System.out.println("Wrong!");
  
            return;
  
        }
  
        for (int i = 0; i < iArr.length; i++) {
  
            if ((strArr[0].charAt(i) ^ iArr[i]) != iArr2[i]) {
  
                z = false;
  
            }
  
        }
  
        if (z) {
  
            System.out.println("Success!");
  
        } else {
  
            System.out.println("Wrong!");
  
        }
  
    }
  
}
```

Флагом будет iArr xor iArr2.
``` Python
iArr = [229, 102, 166, 229, 227, 205, 80, 224, 122, 168, 56, 185, 1, 154, 43, 127, 139, 83, 211, 202, 240]
iArr2 = [212, 7, 249, 175, 162, 155, 49, 191, 62, 247, 90, 214, 108, 248, 116, 62, 255, 60, 190, 163, 155]

flag = ''
for i in range(len(iArr)):
    x = iArr[i] ^ iArr2[i]
    flag += chr(x)
    
print(flag)
```

Ответ: `1a_JAVa_D_bomb_Atomik`