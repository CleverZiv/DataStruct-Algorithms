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

## 3、盛最多水的容器（11）

首尾各一个指针，计算当前能盛多少水

* 计算规则：
指针下标的差 * 首尾指针所指高度矮的
* 指针移动规则：
哪边小，就移动哪边

## 4、3Sum

这道题与 2Sum 的不同在于，我们需要返回所有满足条件的组合，并且是不重复的。我们想复用 2Sum，为了更好地理解解法，我们换一种方式来做 2Sum。

基本思路是：首尾指针，先对数组排序，然后首尾各一个指针，逐渐向中间移动，移动规则：

- 若首尾指针所指的值的和大于 target，则尾指针往前移动
- 若首尾指针所指的值的和小于 target，则首指针往后移动

解答如下：

```go
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

```go
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

```go
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

## 6、next premutation(31)

```go

添加备注


func nextPermutation(nums []int) {
	// 1. 从后往前，找到第一对相邻逆序对，设下标为 i 和 i+1
	i, j := 0, 0
	for i = len(nums) - 2; i >= 0; i-- {
		if nums[i] < nums[i+1] {
			break
		}
	}
	// 2. 从后往前，在[i+1,len-1]找到第一个比 nums[i] 大的数，并交换
	if i >= 0 { // 说明第一步找到了 i
		for j = len(nums) - 1; j > i; j-- {
			if nums[i] < nums[j] {
				break
			}
		}
		swap(&nums, i, j)
	}
	// 3. 此时[i+1,len-1]必为降序数列，将其转换为升序即可
	// 4. 若第一步找不到该逆序对，说明此时数组已经是降序，将其处理为升序即可，执行3
	reverse(&nums, i+1, len(nums)-1)
}

func swap(nums *[]int, i, j int) {
	(*nums)[i], (*nums)[j] = (*nums)[j], (*nums)[i]
}
func reverse(nums *[]int, i, j int) {
	for i < j {
		swap(nums, i, j)
		i++
		j--
	}
}
```
## 7、搜索旋转排序数组（33）
利用二分法，实际题目中已经提示时间复杂度不超过 O(logn)
要注意：
- 判断处于大区间还是小区间，如何判断呢？利用 nums[mid]和num[low]、nums[high]的关系
- 一些边界值的处理，不要忘记“=”
- 最后的 else 逻辑必不可少，否则程序会陷入死循环

```go
func search(nums []int, target int) int {
	// 利用二分搜索，每次二分后判断在哪个区间内
	low, high, mid := 0, len(nums)-1, 0
	for low <= high {
		mid = low + (high-low)>>1
		if nums[mid] == target {
			return mid
		} else if nums[mid] > nums[low] {
			// 在左边的大区间内
			if nums[mid] > target && nums[low] <= target {
				high = mid - 1
			} else {
				low = mid + 1
			}

		} else if nums[mid] < nums[high] {
			// 在右边的小区间内
			if nums[mid] < target && nums[high] >= target {
				low = mid + 1
			} else {
				high = mid - 1
			}
		}else { // 必不可少，当 target 在数组中不存在时，这里的逻辑可以使循环跳出
			if nums[low] == nums[mid] {
				low++
			}
			if nums[high] == nums[mid] {
				high--
			}
		}
	}
	return -1
}
```
## 8、总结所有二分搜索形式（34）
```go
package goleetcode

import "testing"

func TestBinarySearch(t *testing.T) {
	nums := []int{5, 7, 7, 7,7,7,8, 8, 10}
	t.Log(searchRange(nums, 7))
}

/**
二分搜索：
nums 升序排列；无重复元素
*/
func binarySearch(nums []int, target int) int {
	low, high := 0, len(nums)-1
	for low <= high {
		mid := low + (high-low)>>1
		if nums[mid] > target {
			high = mid - 1
		} else if nums[mid] < target {
			low = mid + 1
		} else {
			return mid
		}
	}
	return -1
}

/**
二分搜索：
升序；有重复元素
求第一个等于target的元素的下标
*/
func binarySearchFirstEqual(nums []int, target int) int {
	low, high := 0, len(nums)-1
	for low <= high {
		mid := low + (high-low)>>1
		if nums[mid] > target {
			high = mid - 1
		} else if nums[mid] < target {
			low = mid + 1
		} else {
			// 等于时，如果 nums[mid-1] != target，表明已经是最左边等于 target 的数了
			if mid == 0 || nums[mid-1] != target {
				return mid
			}
			high = mid - 1
		}
	}
	return -1
}

/**
二分搜索：
升序；有重复元素
求最后等于target的元素的下标
*/
func binarySearchLastEqual(nums []int, target int) int {
	low, high := 0, len(nums)-1
	for low <= high {
		mid := low + (high-low)>>1
		if nums[mid] > target {
			high = mid - 1
		} else if nums[mid] < target {
			low = mid + 1
		} else {
			// 等于时，如果 nums[mid+1] != target，表明已经是最左边等于 target 的数了
			if mid == len(nums)-1 || nums[mid+1] != target {
				return mid
			}
			low = mid + 1
		}
	}
	return -1
}

/**
找到第一个大于等于 target 的元素
 */
func binarySearchFirstGreater(nums []int, target int) int {
	low, high := 0, len(nums)-1
	for low <= high {
		mid := low + (high-low)>>1
		if nums[mid] >= target {
			if mid == 0 || nums[mid-1] < target {
				return mid
			}
			high = mid - 1
		} else {
			low = mid + 1
		}
	}
	return -1
}

/**
找到最后一个小于等于 target 的元素
*/
func binarySearchFirstSmaller(nums []int, target int) int {
	low, high := 0, len(nums)-1
	for low <= high {
		mid := low + (high-low)>>1
		if nums[mid] > target {
			high = mid - 1
		} else {
			if mid == len(nums)-1 || nums[mid+1] > target {
				return mid
			}
			low = mid + 1
		}
	}
	return -1
}

/**
找到目标元素在数组中的开始位置和结束位置
 */

func searchRange(nums []int, target int) []int {
	start := binarySearchFirstEqual(nums, target)
	end := binarySearchLastEqual(nums, target)
	return []int{start, end}
}
```
## 9、搜索下一个位置（35）
```go
func searchInsert(nums []int, target int) int {
	low, high := 0, len(nums)
	for low <= high {
		mid := low + (high-low) >> 1
		if nums[mid] > target {
			high = mid-1
		}else if nums[mid] < target {
            // 小于时先判断是否是最大的比 target 小的数，这里要注意 mid == len(nums)-1 的特殊情况
			if mid == len(nums)-1 || nums[mid+1] > target {
				return mid+1
			} else {
				low = mid + 1
			}
		}else {
            // 等于则直接返回
			return mid
		}
	}
	return 0
}
```
## 10、maxmuiem subarray(53)
```go
func maxSubArray(nums []int) int {
	if len(nums) == 0 {
		return 0
	}
	if len(nums) == 1 {
		return nums[0]
	}
	dp, res := make([]int, len(nums)), nums[0]
	dp[0] = nums[0]
	for i := 1; i < len(nums); i++ {
		if dp[i-1] > 0 {
			dp[i] = nums[i] + dp[i-1]
		} else {
			dp[i] = nums[i]
		}
		res = int(math.Max((float64(res)), float64(dp[i])))
	}
	return res
}
```
## 11、组合综合（39）-- 回溯法
回溯法的套路：
```go
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```
```go
func combinationSum(candidates []int, target int) [][]int {
	if len(candidates) == 0 {
		return [][]int{}
	}
	path, res := []int{}, [][]int{}
	sort.Ints(candidates)
	helper(candidates, target, 0, path, &res)
	return res
}

func helper(candidates []int, target int, begin int, path []int, res *[][]int) {
	if target < 0 {
		return
	}
	if target == 0 {
		b := make([]int, len(path))
		copy(b, path)
		*res = append(*res, b)
	}
	// 选择 in 选择列表
	for i := begin; i < len(candidates);i++{
		if candidates[begin] > target {
			// 剪枝优化，这里要求原始数组是有序的。否则 如{2,5,3}，假如此时path为{5},begin=1,target=3,此时会将
			// candidates[1]=5>3，会将其剪掉，但实际上{5,3}是满足的，原因就在于没有升序排列，导致将后面更小的可以组成
			// target 的值给剪掉了
			break
		}
		// 做选择
		path = append(path, candidates[i])
		// 回溯方法主体（选择列表，路径）
		helper(candidates, target-candidates[i], i, path, res)
		// 撤销选择
		path = path[:len(path)-1]
	}
}
```
## 12、组合2
在两处增加去重的逻辑
```go
func combinationSum2(candidates []int, target int) [][]int {
	if len(candidates) == 0 {
		return [][]int{}
	}
	path, res := []int{}, [][]int{}
	sort.Ints(candidates)
	helper2(candidates, target, 0, path, &res)
	return res
}

func helper2(candidates []int, target int, begin int, path []int, res *[][]int) {
	if target < 0 {
		return
	}
	if target == 0 {
		b := make([]int, len(path))
		copy(b, path)
		*res = append(*res, b)
		return
	}
	// 选择 in 选择列表
	for i := begin; i < len(candidates); i++ {
		if i > begin && candidates[i-1] == candidates[i] { // 去重，当本次与上次的值一样时，不取，忽略i==begin时的情况
			continue
		}
		if candidates[i] > target {
			// 剪枝优化，这里要求原始数组是有序的。否则 如{2,5,3}，假如此时path为{5},begin=1,target=3,此时会将
			// candidates[1]=5>3，会将其剪掉，但实际上{5,3}是满足的，原因就在于没有升序排列，导致将后面更小的可以组成
			// target 的值给剪掉了
			break
		}
		// 做选择
		path = append(path, candidates[i])
		// 回溯方法主体（选择列表，路径）
		helper2(candidates, target-candidates[i], i+1, path, res) // 去重，下次取数从 i+1 开始
		// 撤销选择
		path = path[:len(path)-1]
	}
}
```
## 13、首个缺失的正整数（41）
1. 缺失的正整数在给定的数的中间时，利用 map 找到
2. 缺失的正整数在给定的数右边时，直接返回 数组长度+1 的方式找到
```go
func firstMissingPositive(nums []int) int {
	tempMap := make(map[int]int)
	for _, num := range nums {
		tempMap[num] = 1
	}
	for i := 1; i < len(tempMap)+1; i++ { // 要稍大于给定的数组元素
		if _, ok := tempMap[i]; !ok  {
			return i
		}
	}
	return len(tempMap)+1 // 缺失的为数组中最大元素的下一个
}
```
