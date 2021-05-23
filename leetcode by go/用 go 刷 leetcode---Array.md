# 用 go 刷 leetcode---Array

## 1 Two Sum

### 解题思路

这道题最优的做法时间复杂度是 O(n)。

顺序扫描数组，对每一个元素，在 map 中找能组合给定值的另一半数字，如果找到了，直接返回 2 个数字的下标即可。如果找不到，就把这个数字存入 map 中，等待扫到“另一半”数字的时候，再取出来返回结果。

```
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

```
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

## 3、盛最多水的容器（11）

首尾各一个指针，计算当前能盛多少水

- 计算规则： 指针下标的差 * 首尾指针所指高度矮的
- 指针移动规则： 哪边小，就移动哪边

## 4、3Sum

这道题与 2Sum 的不同在于，我们需要返回所有满足条件的组合，并且是不重复的。我们想复用 2Sum，为了更好地理解解法，我们换一种方式来做 2Sum。

基本思路是：首尾指针，先对数组排序，然后首尾各一个指针，逐渐向中间移动，移动规则：

- 若首尾指针所指的值的和大于 target，则尾指针往前移动
- 若首尾指针所指的值的和小于 target，则首指针往后移动

解答如下：

```
// 返回所有值的组合，并且不能重复
func TwoSum2(nums []int, target int) [][]int {
	// 先排序
	sort.Ints(nums)
	// 首尾指针
	low, high := 0, len(nums)-1
	res := make([][]int, 0)
	for low < high {
		if nums[low]+nums[high] > target {
			high--
		} else if nums[low]+nums[high] < target {
			low++
		} else {
			res = append(res, []int{nums[low], nums[high]})
      // 值相同时，跳过
			for low < len(nums)-2 && nums[low] == nums[low+1] {
				low++
			}
			low++
			high--
		}
	}
	return res
}
```

现在我们来看看怎么解决 3Sum 的问题，思路是：先从数组中选定一个数，记为A，然后从数组中剩余的数种找到两个数，使其和为 target - A，可以看到，已经转换为 2Sum 的问题。

但是为了使得返回的组合不重复，我们需要比较每次选定的数的与前一个数的大小关系，如果相同则跳过（该逻辑在上面的 TwoSum2 已经实现，详见注释），代码如下：

```
func threeSum(nums []int, target int) [][]int {
	// 先排序
	sort.Ints(nums)
	// 首尾指针
	low := 0
	res := make([][]int, 0)
	for low < len(nums) {
		twoSumTarget := target - nums[low]
		// 转换为 2sum
		tempNum := nums[low+1:]
		twoSumRess := TwoSum2(tempNum, twoSumTarget)
		for _, twoSumRes := range twoSumRess {
			res = append(res, append(twoSumRes, nums[low]))
		}
		for low < len(nums)-2 && nums[low] == nums[low+1] {
			low++
		}
		low++
	}
	return res
}
```

接下来继续，4Sum，和 3Sum 一样：

```
func fourSum(nums []int, target int) [][]int {
	sort.Ints(nums)
	low := 0
	res := make([][]int, 0)
	for low < len(nums) {
		threeSumTarget := target - nums[low]
		// 转换为 3Sum
		tempNum := nums[low+1:]
		ThreeSumRess := threeSum(tempNum, threeSumTarget)
		for _, threeSumRes := range ThreeSumRess {
			res = append(res, append(threeSumRes, nums[low]))
		}
		for low < len(nums)-2 && nums[low] == nums[low+1] {
			low++
		}
		low++
	}
	return res
}
```

这个思路可以往下解决 kSum 的问题

## 5、3Sum closedSet

这道题是 3Sum 的变体，需要改变的是二分法的判断条件。之前是判断和是否等于 tatget，现在是找所有组合中与 target 距离最小的

```go
import (
	"math"
	"sort"
)
func threeSumClosest(nums []int, target int) int {
	// 先排序
	sort.Ints(nums)
	// 首尾指针
	low := 0
	res := 0
	minGap := math.MaxFloat64
	for low < len(nums) {
		tempGap := 0.0
		twoSumTarget := target - nums[low]
		// 转换为 2sum
		tempNum := nums[low+1:]
		towSumRes := TwoSumCloseSet(tempNum, twoSumTarget)
		tempSum := nums[low] + towSumRes
		tempGap = math.Abs(float64(target - tempSum))
		if minGap > tempGap {
			minGap = tempGap
			res = tempSum
		}
		low++
	}
	return res
}

func TwoSumCloseSet(nums []int, target int) int {
	// 先排序
	sort.Ints(nums)
	// 首尾指针
	low, high := 0, len(nums)-1
	res, minGap := math.MaxInt32, math.MaxInt32
	for low < high {
		tempGap := 0
		tempNum := nums[low]+nums[high]
		if tempNum > target {
			tempGap = tempNum - target
			high--
		} else if tempNum < target {
			tempGap = target - tempNum
			low++
		} else {
			return tempNum
		}
		if minGap > tempGap {
			minGap = tempGap
			res = tempNum
		}
	}
	return res
}
```

## 6、移除数组中重复的数字（26）

使用双指针法：

- p 指针永远指向新数组的最后一位，0~p范围内的数字组成新数组
- q 指针用于遍历旧数组

```go
func removeDuplicates(nums []int) int {
	if len(nums) == 0 {
		return 0
	}
	p, q := 0, 0
	for q < len(nums)-1 {
		if nums[q] == nums[q+1] {
			q++
		} else {
			nums[p+1] = nums[q+1]
			p++
			q++
		}
	}
	return p + 1
}
```

