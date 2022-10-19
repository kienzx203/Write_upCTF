# **WRITE UP MINICTF** #

## REVERSE ENGINEERING - **EZ RE** ##

>## Source Code ##

```C
#include <stdio.h>
#include <string.h>
int main()
{
    printf("-------------------CUOC_BAT_BAT_DAU-----------------------------\n");
    printf("Input Flags: ");
    char flag[33] = {};
    puts(flag);
    fgets(flag, sizeof(flag), stdin);
    if (strlen(flag) == 32 &&
        flag[0] == 'd' &&
        flag[29] == '3' &&
        flag[4] == 'r' &&
        flag[2] == '5' &&
        flag[23] == 'r' &&
        flag[3] == 'c' &&
        flag[17] == '4' &&
        flag[1] == '3' &&
        flag[7] == 'b' &&
        flag[10] == '_' &&
        flag[5] == '4' &&
        flag[9] == '3' &&
        flag[11] == 't' &&
        flag[15] == 'c' &&
        flag[8] == 'l' &&
        flag[12] == 'H' &&
        flag[20] == 'c' &&
        flag[14] == '_' &&
        flag[6] == 'm' &&
        flag[24] == '5' &&
        flag[18] == 'r' &&
        flag[13] == '3' &&
        flag[19] == '4' &&
        flag[21] == 'T' &&
        flag[16] == 'H' &&
        flag[27] == 'f' &&
        flag[30] == 'b' &&
        flag[25] == '_' &&
        flag[22] == '3' &&
        flag[28] == '6' &&
        flag[26] == 'f' && 
        flag[31] == '0')
    {
        printf("ISPCTF{");
        printf("%s", flag);
        printf("}");
    }
    else
        printf("Incorrect!!!!");
}
```

>## **Overview & Idea** ##

- Ta thấy đề cho là một mảng flag có độ dài len(flag) = 32 chưa được sắp xếp vậy ý tưởng bài này là mình hãy chạy sắp xếp và chạy code ra flag như sau :

```C++
#include <iostream>

using namespace std;

int main() {
char flag[200];
string s="ISPCTF{";
flag[0] = 'd'; 
flag[29] = '3' ;
flag[4] = 'r' ;
flag[2] = '5' ;
flag[23] = 'r' ;
flag[3] = 'c' ;
flag[17] = '4';
flag[1] = '3' ;
flag[7] = 'b' ;
flag[10] = '_' ;
flag[5] = '4' ;
flag[9] = '3' ;
flag[11] = 't' ;
flag[15] = 'c' ;
flag[8] = 'l' ;
flag[12] = 'H' ;
flag[20] = 'c' ;
flag[14] = '_' ;
flag[6] = 'm' ;
flag[24] = '5' ;
flag[18] = 'r' ;
flag[13] = '3' ;
flag[19] = '4' ;
flag[21] = 'T' ;
flag[16] = 'H' ;
flag[27] = 'f' ;
flag[30] = 'b' ;
flag[25] = '_' ;
flag[22] = '3' ;
flag[28] = '6' ;
flag[26] = 'f'  ;
flag[31] = '0';
for (int i=0;i<=31;i++)
    s+=flag[i];
s+="}";
cout<<s;
    return 0;
}
//Flag=ISPCTF{d35cr4mbl3_tH3_cH4r4cT3r5_ff63b0}
```

## REVERSE ENGINEERING - **Loop Key** ##

>## Source Code ##

```C++
#include <iostream>
using namespace std;
int check_flag(int data[])
{
    int a[] = {
        73,167,324,545,1360,2265,7908,133,160,277,860,1705,3696,
        6249,303,433,452,1097,2084,3401,6672,538,704,909,1520,2497,
        2244,6809,833,941,1096,1353,1824,2625,4228,1276,1546};
    for (int i = 0; i < 37; i++)
    {
        if (data[i] != a[i])
            return 0;
    }
    return 1;
}
int s_203_key(string data)
{
    int src[100];
    for (int i = 0; i < data.length(); i++)
    {
        src[i] = (((int)data[i] << ((char)i % 7)) + i * i);
    }
    check_flag(src);
}
int main()
{
    string flag;
    cout << "_______________BAT_DAU_______________" << endl;
    cout << "Inputflag : ";
    cin >> flag;
    if (s_203_key(flag) == 1)
        cout << "correct!!!";
    else
        cout << "incorrect!!!";
}
```
>## **Overview & Idea** ##


- Theo như ta thấy để được in ra correct!!! thì sau khi encrypt input flag phải bằng với các phần tử mảng a trong hàm `check_flag()` . Vậy trước hết mình phải hiểu về thuật toán của hàm `s_203_key()` .

- Ta thấy thuật toán ở đây tác giả dùng phép dịch bit sang trái sau đó cộng thêm phần tử i * i để encrypt input mình nhập vào rồi so sánh với data `mảng a`. Nếu bằng với `mảng a` thì điều đó có nghĩa input mình nhập vào là flag. Từ đó, chúng ta hãy dùng data `mảng a` để decrypt bằng phép trừ i * i và say đó dịch bit sang phải .

```C++
#include <iostream>
using namespace std;

int main(){
   int a[] = {
    73,167,324,545,1360,2265,7908,133,160,277,860,1705,3696,
    6249,303,433,452,1097,2084,3401,6672,538,704,909,1520,2497,
    2244,6809,833,941,1096,1353,1824,2625,4228,1276,1546};
    int flag[36];
    int result[36];
    for (int i = 0; i< 37; i++){
       flag[i] = a[i] - i*i;
    }
    for (int i =0; i < 37; i++){
        result[i] =  flag[i] >>  ((char)i %7);
        printf("%c", result[i]);
    }
}
//flag=ISPCTF{T01_co_kh1en_ban_vu1_12112003}
```




