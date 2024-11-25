# A.监测点查询

[题目链接](https://www.acwing.com/problem/content/description/3295/)

比较简单的题目，但还是有几点需要注意。。。

## Points

### 1. PII的比较大小（应用于优先队列）
```
bool cmp(const PII& l, const PII& r) {
    return l.first < r.first;
}
```

### 2. 小头优先队列的定义
```
priority_queue<PII, vector<PII>, greater<PII>> distances;
```

### 3. 按顺序输出优先队列
因为优先队列不支持随机访问，故需要访问一个`pop()`一个
```
    for(int i=0; i<3; i++) {
        printf("%d\n", distances.top().second);
        distances.pop();
    }
```
