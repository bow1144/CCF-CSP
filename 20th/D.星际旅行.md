# D.星际旅行

> 被IO卡了半节课，注意检查IO

[题目链接](https://www.acwing.com/problem/content/3298/)

* 题目类型：计算几何，但还比较简单？
* 题目大意：`n`维空间中有`m`个点和一个半径为`R`的黑洞，定义距离为两点间最短距离为**不经过黑洞的最短欧几里得距离**

<img width="398" alt="{D919D967-AB94-417E-A803-13D829EBCBCA}" src="https://github.com/user-attachments/assets/941476d5-4764-40bf-a6dc-8076bf344d5d">

## 思路
### 1. 判断两点之间是否经过黑洞
* 预先求出两点、黑洞三者之间的距离
* 连接两点与黑洞构成三角形，用*海伦公式*求出面积和高

### 2. 分类讨论：如果不经过黑洞
* 分类依据：
  - 两点所连直线不经过黑洞，必然不经过黑洞
  - 直线经过黑洞：判断是否是以OA/OB为长边的钝角三角形，如果是则不经过黑洞
  - 其余情况经过黑洞
* 如果不经过黑洞，直接求解欧几里得距离即可

### 3. 分类讨论：不经过黑洞
* 距离=两条切线+弧长
* 切线距离：勾股定理
* 弧长：由余弦定理+三角关系计算得弧角

## 程序
### 1. 距离计算
```
double distance(vector<double>& a, vector<double>& b, double R) {
    // A2表示A到原点距离平方，AB2表示AB两点之间距离平方
    double disA2 = 0, disB2 = 0, disAB2 = 0;
    int dim = a.size();
    for(int i=0; i<dim; i++) {
        disA2 += a[i] * a[i];
        disB2 += b[i] * b[i];
        disAB2 += (a[i]-b[i]) * (a[i]-b[i]);
    }
    
    double disA = sqrt(disA2);
    double disB = sqrt(disB2);
    double disAB = sqrt(disAB2);

    // 海伦公式计算高
    double p = (disA + disB + disAB) / 2;
    double S = sqrt(p * (p-disA) * (p-disB) * (p-disAB));
    double h = (S * 2) / disAB;

    // 判断：高大于半径或构成OA/OB长边钝角三角形
    if(h>=R || disA2+disAB2<=disB2 || disB2+disAB2<=disA2) return disAB;

    // 两个切线长度
    double Ap = sqrt(disA2 - R*R);
    double Bp = sqrt(disB2 - R*R);

    // cosAngle：角AOB的余弦值，用余弦定理计算
    double cosAngle = (disA2 + disB2 - disAB2) / (2 * disA * disB);
    cosAngle = max(-1.0, min(1.0, cosAngle));
    // angle弧角
    double angle = acos(cosAngle) - acos(R/disA) - acos(R/disB);
    double rad = R * angle;
    double ret = Ap + Bp + rad;
    
    return ret;
}
```

### 2. 主函数
```
int main(void) {
    int dim, num;
    double R;
    scanf("%d %d", &dim, &num);
    scanf("%lf", &R);

    // 存储黑洞位置
    vector<double> black(dim);
    for(int i=0; i<dim; i++) scanf("%lf", &black[i]);
    
    vector<vector<double>> points(num, vector<double>(dim));
    for (int i = 0; i < num; i++) {
        for (int j = 0; j < dim; j++) {
            scanf("%lf", &points[i][j]);
            points[i][j] -= black[j]; // 平移到以黑洞为原点的坐标系中
        }
    }
    
    for(int i=0; i<num; i++) {
        double sum = 0;
        for(int j=0; j<num; j++) {
            if(i == j) continue;
            sum += distance(points[i], points[j], R);
        } // 注意保留14位小数
        printf("%.14lf\n", sum);
    }
    
    return 0;
}
```

## Points
* 当你确认逻辑正确但死活检查不出的时候，检查IO
* 计算几何一定要画草稿
* 高小于半径不一定是折线，还有可能是钝角三角形
