## 普通哈希

哈希表一般用语言自带的库，但当不涉及kv对（就是单纯用set）并对时间复杂度有要求时，可以用数组手写模拟

```golang
// list存的是每个hash拉下来的拉链链表，简单来说就是用idx将list分成好几段，每段给一个hashtable[i]使用
// next和list对应，next[i]代表list[i]指向的下一个位置
// 一般N取距离题目要求最大范围最近且大于题目要求的第一个质数，这么做的原因是hash冲突概率会最小
var HashTable [N]int
var List[N] int
var Next [N]int
var idx int

// 初始化时需要将Hash表中的元素初始化成一个题目中绝不会出现的值，不一定是模版里的-1
func Init() {
    for i:=0;i<N;i++ {
        HashTable[i] = -1
    }
}

// 头插+取模
// 取模这么取是为了防止x是负数，直接abs也不行，因为abs(-10%3)和10%3的结果不一样，这个直接背就可以，不用管原理
func Insert(x int) {
    k := (x%N+N)%N
    List[idx] = x
    Next[idx] = HashTable[k]
    HashTable[k] = idx
    idx += 1
}

// 查询
func Qurey(x int) bool {
    k := (x%N+N)%N
    for i:=HashTable[k];i!=-1;i=Next[i] {
        if List[i] == x {
            return true
        }
    }
    return false
}
```

## 字符串哈希

字符串哈希一般用于判断contains，这个比kmp还快，可以做到O(1)的复杂度

一般使用一个P进制的权重和字符串中的每个字符相乘进行hash

例如：

abcd -> 设为 1234

则abcd的hash为 (1*p^3 + 2*p^2 + 3*p^1 + 4*p^0) mod q

q = 2^64

P = 131 或 13331 这个是工程经验，没什么道理，取这两个值时冲突会最小化 

如果q足够大，则没有机会mod q，所以一般当s[i]的占位为long时即可(go中使用int64啥的就行)

总结：

hash(s) = 累加{s[i] * p^(len(s)-1-i)} mod 2^64

字符串hash中有几个公式要记一下

```golang
var HashTable [N]int
// pList代表p中每一项的值，提前存好方便O(1)复杂度的查询和修改
var pList [N]int

// 初始化
// pList[0]不能为0，因为0乘任何数都为0......
func Init() {
    pList[0] = 1
}

// 公式1.获取r到l这个范围内字符串的hash值 
func Get(l,r int) int {
    return HashTable[r] - HashTable[l-1] * pList[r-l+1]
}

// 公式2.将字符串s入表
func Set(s string) {
    for i:=1;i<len(s)+1;i++ {
        pList[i] = pList[i-1]*P
        HashTable[i] = HashTable[i-1]*P+int(s[i-1])
    }
}

```

例题 AcWing 841. 字符串哈希

```golang
package main

import "os"
import "fmt"
import "bufio"

const (
    N = 100010
    P = 131
)

var HashTable [N]int
var pList [N]int

func Init() {
    pList[0] = 1
}

func Get(l,r int) int {
    return HashTable[r] - HashTable[l-1] * pList[r-l+1]
}

func Set(s string) {
    for i:=1;i<len(s)+1;i++ {
        pList[i] = pList[i-1]*P
        HashTable[i] = HashTable[i-1]*P+int(s[i-1])
    }
}

func main() {
    in,out := bufio.NewReader(os.Stdin),bufio.NewWriter(os.Stdout)
    defer out.Flush()
    var n,m,l1,r1,l2,r2 int
    var s string
    fmt.Fscan(in,&n,&m,&s)
    Init()
    Set(s)
    for i:=0;i<m;i++ {
        fmt.Fscan(in,&l1,&r1,&l2,&r2)
        if Get(l1,r1) == Get(l2,r2) {
            fmt.Fprintln(out,"Yes")
        }else {
            fmt.Fprintln(out,"No")
        }
    }
}
```

