# B. 风险人群筛查

[题目链接](https://www.acwing.com/problem/content/3296/)

一道小模拟罢了，唯一值得纪念的是连续记录的用法
## code
```
# include <bits/stdc++.h>
using namespace std;

int n, k, t, xl, xr, yd, yu;
int stay_num = 0, pass_num = 0;

int main(void) {
    scanf("%d %d %d", &n, &k, &t);
    scanf("%d %d %d %d", &xl, &yd, &xr, &yu);
    
    while(n--) {
        int constrate = 0, max_c = 0;
        bool pass = false; 
        
        for(int i=0; i<t; i++) {
            int x=0, y=0;
            scanf("%d %d", &x, &y);
            if(x>=xl && x<=xr && y>=yd && y<=yu) {
                pass = true;
                constrate++;
                max_c = max(constrate, max_c);
            }
            else constrate = 0;
        }
        if(max_c >= k) stay_num++;
        if(pass) pass_num++;
    }
    
    printf("%d\n%d\n", pass_num, stay_num);
    return 0;
}
```

## points
### 连续性变量
```
while(...) {
  if(中途中断) : constrate = 0
  else : constrate++
}
```
