# A.报数

[题目链接](https://www.acwing.com/problem/content/3285/)

作为A题比较简单

## 代码
### AC100

```
# include <bits/stdc++.h>
using namespace std;

vector<int> skip_num(4, 0);
int n;

bool is_skip(int n) {
    if(n % 7 == 0) return true;
    while(n) {
        int x = n % 10;
        if(x == 7) return true;
        n = n / 10;
    }
    return false;
}

int main(void) {
    scanf("%d", &n);
    int st = 1, num = 0;
    while(num < n){
        if(is_skip(st)) {
            skip_num[(st-1) % 4]++;
        } 
        else {
            num++;
        }
        st++;
    }
    
    for(int i=0; i<4; i++) {
        printf("%d\n", skip_num[i]);
    }
    return 0;
}
```

## points
* `skip_num[(st-1) % 4]++;`中注意是`st-1`，不能写成`num-1`，否则会TLE
