# E. 星际旅行

* AC20：暴力
* AC40：加查线段树
* AC60：加乘查线段树
* AC80：转子加乘查线段树
* AC：离散化线段树
* 本题做60分性价比较高

## 线段树模板（加乘查）
```
class SegmentTree {
public:
    SegmentTree(int n) {
        size = n;
        tree.resize(4 * n);
        lazy_add.resize(4*n, 0);
        lazy_mul.resize(4*n, 1);
    }
    
    ll query (int l, int r) {
        return query(1, 0, size-1, l, r);
    }
    
    void update_add(int l, int r, ll val) {
        update_add(1, 0, size-1, l, r, val);
    }
    
    void update_mul(int l, int r, ll val) {
        update_mul(1, 0, size-1, l, r, val);
    }
    
private:
    int size;
    vector<ll> tree;
    vector<ll> lazy_add;
    vector<ll> lazy_mul;
    
    void push_down(int node, int nl, int nr) {
        if(lazy_mul[node]!=1 || lazy_add[node]!=0) {
            int mid = (nl + nr) / 2;
            apply(node*2, nl, mid, lazy_mul[node], lazy_add[node]);
            apply(node*2+1, mid+1, nr, lazy_mul[node], lazy_add[node]);
        
            lazy_mul[node] = 1;
            lazy_add[node] = 0;
        }
    }
    
    void apply(int node, int nl, int nr, ll mul, ll add) {
        tree[node] = tree[node] * mul % mod + 
            (nr - nl + 1) * add % mod; 
    
        if(nl != nr) {
            lazy_mul[node] = lazy_mul[node] * mul % mod;
            lazy_add[node] = (lazy_add[node] * mul + add) % mod;
        }
    }
    
    ll query(int node, int nl, int nr, int l, int r) {
        if(l > r) return 0;
        push_down(node, nl, nr);
        if(l==nl && r==nr) return tree[node];
        
        int mid = (nl + nr) / 2;
        return (query(node*2, nl, mid, l, min(r, mid)) + 
               query(node*2+1, mid+1, nr, max(l, mid+1), r)) % mod;    
    }
    
    void update_add(int node, int nl, int nr, int l ,int r, ll val) {
        if(l > r) return;
        push_down(node, nl, nr);
        
        if(l==nl && r==nr) {
            apply(node, nl, nr, 1, val);
            return;
        }
        
        int mid = (nl + nr) / 2;
        update_add(node*2, nl, mid, l, min(r, mid), val);
        update_add(node*2+1, mid+1, nr, max(mid+1, l), r, val);
        
        tree[node] = (tree[node*2] + tree[node*2+1]) % mod;
    }
    
    void update_mul(int node, int nl, int nr, int l, int r, ll val) {
        if(l > r) return;
        push_down(node, nl, nr);
        
        if(l==nl && r==nr) {
            apply(node, nl, nr, val, 0);
            return;
        }
        
        int mid = (nl + nr) / 2;
        update_mul(node*2, nl, mid, l, min(r, mid), val);
        update_mul(node*2+1, mid+1, nr, max(mid+1, l), r, val);
        
        tree[node] = (tree[node*2] + tree[node*2+1]) % mod;
    }
};
```
