## 一维前缀和

前缀和是一种设计方法，不是一种单独的算法，可以提高代码效率

设si为前缀和数组，ai为数据数组

前缀和的数据数组一般会以下标1开始，下标0置为0，方便求n个前缀和

s[i] = 0+a[1]+a[2]+...+a[i]

s[i] = s[i-1]+a[i]

求a中l到r下标之和 = s[r] - s[l-1] 或者 s[r+1] - s[l] 主要看下标是从0开始还是从1开始

特别：如果求a中前n个下标之和 = s[n]

例题 AcWing 795. 前缀和

```golang
package main

import "fmt"

func main() {
    var n int
    var m int
    fmt.Scanf("%d",&n)
    fmt.Scanf("%d",&m)
    nums := make([]int,n+1)
    for i:=1;i<n+1;i++ {
        fmt.Scanf("%d",&nums[i])
    }
    b := make([]int,n+1)
    b [0] = 0
    for i:=1;i<n+1;i++ {
        b[i] = nums[i] + b[i-1]
    }
    for i:=0;i<m;i++ {
        var i int
        var j int
        fmt.Scanf("%d",&i)
        fmt.Scanf("%d",&j)
        fmt.Printf("%d\n",b[j]-b[i-1])
    }
}
```

## 二维前缀和

想画图一样思考就可以了，常用于计算面积

sij为二维前缀和数组，aij为数据数组

s[i][j] = s[i-1][j] + s[i][j-1] - s[i-1][j-1] + a[i][j]

a[i][j] = s[i][j] - s[i-1][j] - s[i][j-1] + s[i-1][j-1]

例题 AcWing 796. 子矩阵的和

```golang
package main

import "fmt"
import "bufio"
import "os"

func main() {
    var n,m,q int
    in := bufio.NewReader(os.Stdin)
    out := bufio.NewWriter(os.Stdout)
    fmt.Scanf("%d %d %d",&n,&m,&q)
    defer out.Flush()

    mat := [1010][1010]int{}
    b := [1010][1010]int{}
    for i:=1;i<n+1;i++ {
        for j:=1;j<m+1;j++ {
            _,_ = fmt.Fscan(in,&mat[i][j])
        }
    }
    for i:=1;i<n+1;i++ {
        for j:=1;j<m+1;j++ {
            b[i][j] = b[i-1][j] + b[i][j-1] + mat[i][j] - b[i-1][j-1]
        }
    }

    for i:=0;i<q;i++ {
        var x1,y1,x2,y2 int
        fmt.Fscan(in,&x1,&y1,&x2,&y2)
        res := b[x2][y2] - b[x1-1][y2] - b[x2][y1-1] + b[x1-1][y1-1]
        fmt.Fprintf(out,"%d\n",res)
    }
}
```

## 一维差分

差分是一个数组，若数组a满足

a[i] = b[1]+b[2]+...+b[i]

则数组b是a的差分，a是b的前缀和

用处：
  - 使用O(n)的时间用b数组反推出a数组
  - 用O(1)的时间求出a[i]+c (i=1...n)

比如a[l]到a[r]全部+c，可以让b[l]+c，由于a[l]到a[n]可以通过b数组推出，所以相当于a[l]到a[n]全部+c，而r到n的部分只要-c，就可以使得只有a[l]到a[r]部分+c，操作时间复杂度为O(1)

由于差分数组不需要构造，可以通过原数组进行推到得出，比如

a = [1,2,4]

b[1] = a[1] - 0

b[2] = a[2] - a[1]

b[3] = a[3] - a[2]

所以差分数组唯一的操作就是 +c 和 -c

由于a可以通过b推倒得出，所以差分数组的操作时间复杂度为O(1)，即对a[l]到a[r]之间的数全部+c时不需要对b[l]到b[r]之间的数都+c，只需要做b[l]+c和b[r+1]-c的操作即可

后面每次使用a数组直接通过b数组更新a数组推导即可得出新的a数组

a[i] = a[i-1]+b[i]

例题 AcWing 797. 差分

```golang
package main

import "fmt"
import "bufio"
import "os"

func insert() {

}

func main() {
    in := bufio.NewReader(os.Stdin)
    out := bufio.NewWriter(os.Stdout)
    defer out.Flush()

    var n,q int
    fmt.Fscan(in,&n,&q)
    nums := [100010]int{}
    b := [100010]int{}
    for i:=1;i<n+1;i++ {
        fmt.Fscan(in,&nums[i])
        b[i] = nums[i] - nums[i-1]
    }
    for i:=0;i<q;i++ {
        var l,r,c int
        fmt.Fscan(in,&l,&r,&c)
        b[l] += c
        b[r+1] -= c
    }
    for i:=1;i<n+1;i++ {
        nums[i] = nums[i-1] + b[i]
        fmt.Fprintf(out,"%d ",nums[i])
    }
}
```

## 二维差分

直接上公式 

设a为原矩阵，b为差分矩阵

构造b: b[i][j] = a[i][j] - a[i-1][j] - a[i][j-1] + a[i-1][j-1]

差分操作 ：从x1,y1 到 x2,y2 的范围内对a进行+c操作

b[x1][y1] += c
b[x2+1][y2+1] += c
b[x2-1][y1] -= c
b[x1][y2-1] -= c

推出a

a[i][j] = b[i][j] + a[i-1][j] + a[i][j-1] - a[i-1][j-1]



