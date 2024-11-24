# B.回收站选址

[题目链接](https://www.acwing.com/problem/content/3286/)

本题是一道空间优化题目（其实不需要优化，没A100的原因是不会二维哈希）

## AC50

* 考虑到每个垃圾桶都出现在垃圾的上下左右，容易选出备选垃圾桶点`O(n)`
* 遍历备选垃圾桶，找到合法垃圾桶`O(n)`
* 遍历合法垃圾桶，算出分数`O(1)`
* 这里看错题了，看漏了“垃圾桶点必须有垃圾”

```
# include <bits/stdc++.h>
using namespace std;

typedef pair<int, int> PII;

vector<PII> rubbish;
vector<PII> may_boxes;
vector<int> points(5, 0);
set<PII> boxes;

bool plat[1005][1005];

int get_score(PII box) {
    int score = 0;
    int x = box.first, y = box.second;
    if(x && y && plat[x-1][y-1]) score++;
    if(x && plat[x-1][y+1]) score++;
    if(y && plat[x+1][y-1]) score++;
    if(plat[x+1][y+1]) score++;
    return score;
}

int main(void) {
    int n;
    scanf("%d", &n);
    while(n--) {
        int x, y;
        scanf("%d %d", &x, &y);
        rubbish.emplace_back(x, y);
        may_boxes.emplace_back(x-1, y);
        may_boxes.emplace_back(x, y-1);
        may_boxes.emplace_back(x+1, y);
        may_boxes.emplace_back(x, y+1);
        plat[x][y] = true;
    }
    
    for(auto may_box : may_boxes) {
        int x = may_box.first, y = may_box.second;
        if(x&&y&&plat[x-1][y]&&plat[x][y-1]&&plat[x+1][y]&&plat[x][y+1]) 
            boxes.insert(may_box);
    }
    
    for(auto box : boxes) {
        points[get_score(box)]++;
    }
    
    for(auto point : points) {
        printf("%d\n", point);
    }
    
    return 0;
}
```

* 为了判断合法垃圾桶，需要一个地图存储所有垃圾
* 二维`bool`数组的最大容量约为`2000*2000`
* `unordered_set`使用哈希函数，没有`PII`对应的哈希，解决方法有两种：
  - 使用`set`
  - 自定义哈希函数

### AC100
* 用哈希表存储垃圾站
* `unordered_set`没有PII的哈希方式，要自定义哈希
* 在集合中查找：`if(garbage.find({x, y}) != garbage.end())`
* 在集合中计数：`int cnt = garbage.count({x, y})`

```
# include <bits/stdc++.h>
using namespace std;

struct Point {
    int x, y;
    bool operator==(const Point& other)const {
        return x==other.x && y==other.y;
    }
    
    struct Hash {
        size_t operator()(const Point& p)const {
            int hash_x = hash<int>()(p.x);
            int hash_y = hash<int>()(p.y);
            return hash_x ^ (hash_y*31);
        }
    };
};

int main(void) {
    int n;
    scanf("%d", &n);
    unordered_set<Point, Point::Hash> garbages;
    vector<int> scores(5, 0);
    
    while(n--) {
        int x, y;
        scanf("%d %d", &x, &y);
        garbages.insert({x, y});
    }
    
    for(auto& p : garbages) {
        int x = p.x, y = p.y;
        if(garbages.count({x+1, y})&&
           garbages.count({x-1, y})&&
           garbages.count({x, y+1})&&
           garbages.count({x, y-1})) {
               
               int score = 0;
               if(garbages.count({x-1, y-1})) score++;
               if(garbages.count({x-1, y+1})) score++;
               if(garbages.count({x+1, y-1})) score++;
               if(garbages.count({x+1, y+1})) score++;
               scores[score]++;
           }
    }
    
    for(int i=0; i<5; i++) printf("%d\n", scores[i]);
    return 0;
}
```

## Points
### 集合存点模板
#### 二维点
```
struct Point {
    int x, y;
    bool operator==(const Point& other)const {
        return x==other.x && y==other.y;
    }
    
    struct Hash {
        size_t operator()(const Point& p)const {
            int hash_x = hash<int>()(p.x);
            int hash_y = hash<int>()(p.y);
            return hash_x ^ (hash_y*31);
        }
    };
};
```

### 三维点
```
struct Point3D {
    int x, y, z;
    
    // 重载==运算符用于比较三维点
    bool operator==(const Point3D& other) const {
        return x == other.x && y == other.y && z == other.z;
    }
    
    // 自定义哈希函数
    struct Hash {
        size_t operator()(const Point3D& p) const {
            // 使用 XOR 组合哈希值
            size_t hash_x = hash<int>()(p.x);
            size_t hash_y = hash<int>()(p.y);
            size_t hash_z = hash<int>()(p.z);
            
            // 组合各个坐标的哈希值
            return hash_x ^ (hash_y << 1) ^ (hash_z << 2);  // 移动并 XOR 操作，减少冲突
        }
    };
};
```

## 集合插找点
* `garbage.insert({x, y})`
* `if(garbage.find({x, y}) != garbage.end())`
* `int cnt = garbage.count({x, y});`
