kmp一般只用作字符串匹配，匹配可以是contains，也可以要求返回start或者end下标

kmp分为创建next数组和匹配两部分，其中创建next数组是死模版

```golang
func GetNext(s string) []int {
	j := 0
	next := make([]int,len(s))
	next[0] = 0
	for i:=1;i<len(s);i++ {
		for j > 0 && s[i] != s[j] {
			j = next[j-1]
		}
		if s[i] == s[j] {
			j += 1
			next[i] = j
		}
	}
	return next
}
```

匹配

匹配时一般只移动模式串，不移动原串

```golang
// s为原字符串，p为模式串，一般next数组都是由p获取
// s的下标i每次移动一格，不会回退，回退的只有p
 next := GetNext(p)
 for i,j:=0,0;i<m;i++ {
     for j>0 && s[i] != p[j] {
         j = next[j-1]
     }
     if s[i] == p[j] {
         j += 1
     }
     // 匹配成功，可根据题目要求写匹配成功后的逻辑
     // 这里是返回start位置
     if j == n {
         fmt.Fprintf(out,"%d ",i-n+1)
         j = next[j-1]
     } 
 }
```

