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

## 2、寻找两个正序数组的中位数（4）

用于分析的测试用例：

```go
// 奇数
nums1 := []int {2,4,6,7}
nums2 := []int {1,3,4,8,9}
// 偶数
nums1 := []int {2,4,6,7，10}
nums2 := []int {1,3,4,8,9}
// nums1 过小，中位数的左边在 nums2
nums1 := []int {1}
nums2 := []int {2,3,4}
// nums1 过大，中位数右边在 nums2
nums1 := []int{5}
nums2 := []int{1,2}
```

