# E.魔数
[题目链接](https://www.acwing.com/problem/content/description/3289/)

本题使用`python3`进行大数处理，但不确定考试环境中是否可以使用线段树扩展

## AC25：暴力
```
U = [314882150829468584,
    427197303358170108,
    1022292690726729920,
    1698479428772363217,
    2006101093849356424]
    
def f(x):
    return (x % 2009731336725594113) % 2019
    
n, q = map(int, input().split())
A = list(range(1, n+1))

for _ in range(q):
    l, r = map(int, input(). split())
    s = sum(f(A[i]) for i in range(l-1, r))
    print(s)
    
    t = s % 5
    for i in range(l-1, r):
        A[i] *= U[t]
```

## AC100：惰性线段树：悬而未决
> 不对，好像不能开惰性线段树，这题悬而未决
```
class SegmentTree:
    def __init__(self, n):
        self.n = n
        self.tree = [1] * (4 * n)  # 线段树数组，初始化为1
        self.lazy = [1] * (4 * n)  # 懒标记数组，初始化为1
    
    # 建树，初始化线段树
    def build(self, start, end, node, arr):
        if start == end:
            self.tree[node] = f(arr[start])  # 叶子节点存储 f(A[i])
        else:
            mid = (start + end) // 2
            left_child = 2 * node + 1
            right_child = 2 * node + 2
            self.build(start, mid, left_child, arr)
            self.build(mid + 1, end, right_child, arr)
            self.tree[node] = self.tree[left_child] * self.tree[right_child]  # 乘法合并
    
    # 懒标记的下推操作
    def propagate(self, start, end, node):
        if self.lazy[node] != 1:
            # 更新当前节点的值
            self.tree[node] *= self.lazy[node]
            if start != end:  # 如果不是叶子节点，向子节点下推懒标记
                left_child = 2 * node + 1
                right_child = 2 * node + 2
                self.lazy[left_child] *= self.lazy[node]  # 保持乘法更新
                self.lazy[right_child] *= self.lazy[node]
            self.lazy[node] = 1  # 清除当前节点的懒标记
    
    # 区间更新：给区间 [L, R] 中的每个元素乘以 val
    def update_range(self, start, end, node, L, R, val):
        self.propagate(start, end, node)  # 先处理懒标记
        if start > R or end < L:  # 当前节点区间和查询区间无交集
            return
        if start >= L and end <= R:  # 当前节点区间完全在查询区间内
            self.lazy[node] *= val  # 延迟更新懒标记
            self.propagate(start, end, node)  # 更新当前节点
            return
        # 部分重叠：递归更新左右子树
        mid = (start + end) // 2
        left_child = 2 * node + 1
        right_child = 2 * node + 2
        self.update_range(start, mid, left_child, L, R, val)
        self.update_range(mid + 1, end, right_child, L, R, val)
        self.tree[node] = self.tree[left_child] * self.tree[right_child]  # 乘法合并
    
    # 区间查询：查询区间 [L, R] 的乘积
    def query_range(self, start, end, node, L, R):
        self.propagate(start, end, node)  # 处理懒标记
        if start > R or end < L:  # 当前节点区间和查询区间无交集
            return 1  # 返回 1，因为乘法单位元素是 1
        if start >= L and end <= R:  # 当前节点区间完全在查询区间内
            return self.tree[node]
        # 部分重叠：递归查询左右子树
        mid = (start + end) // 2
        left_child = 2 * node + 1
        right_child = 2 * node + 2
        left_product = self.query_range(start, mid, left_child, L, R)
        right_product = self.query_range(mid + 1, end, right_child, L, R)
        return left_product * right_product  # 乘法合并


# U 数组和 f 函数定义
U = [
    314882150829468584,
    427197303358170108,
    1022292690726729920,
    1698479428772363217,
    2006101093849356424
]

def f(x):
    return (x % 2009731336725594113) % 2019

def solve():
    # 读取 n 和 q
    n, q = map(int, input().split())
    
    # 初始化数组 A
    A = list(range(1, n + 1))  # A[i] = i + 1
    
    # 构建线段树
    st = SegmentTree(n)
    st.build(0, n - 1, 0, A)
    
    # 处理 q 次查询
    for _ in range(q):
        l, r = map(int, input().split())
        
        # 查询区间和
        s = st.query_range(0, n - 1, 0, l - 1, r - 1)
        print(s)
        
        # 计算 t = s % 5
        t = s % 5
        
        # 更新区间 [l, r]，每个元素乘以 U_t
        st.update_range(0, n - 1, 0, l - 1, r - 1, U[t])

# 调用 solve 函数
solve()
```
