# 用 go 刷 leetcode---Array

## 1 Two Sum

### 解题思路

这道题最优的做法时间复杂度是 O(n)。

顺序扫描数组，对每一个元素，在 map 中找能组合给定值的另一半数字，如果找到了，直接返回 2 个数字的下标即可。如果找不到，就把这个数字存入 map 中，等待扫到“另一半”数字的时候，再取出来返回结果。

```go
func twoSum(nums []int, target int) []int {
    map1 := make(map[int]int)
    for i1, v := range nums {
        temp := target - v
        if i2, ok := map1[temp]; ok {
            return []int{i1, i2}
        } else {
            map1[v] = i1
        }
    }
    return nil
}
```

