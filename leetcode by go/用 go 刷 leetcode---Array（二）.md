# 用 go 刷 leetcode---Array（二）

## 1、跳跃游戏1（55）

只是判断是否能够成功跳到最后一个数字，那只需要逐个跳就行了。但逐个跳的前提是起跳点是合法的，即起跳点可以被前面的起跳点到达。表现为：当前能到达的最大位置，小于当前正在遍历的元素的下标

```go
func jump(nums []int) bool {
	maxPosition := 0
	for i := 0; i < len(nums); i++ {
		if i > maxPosition { // 当前能到达的最大位置，大于当前正在遍历的元素的下标，说明该起跳点到达不了，返回失败
			return false
		}
		maxPosition = int(math.Max(float64(maxPosition), float64(i+nums[i])))
	}
	return true
}
```

## 2、跳跃游戏2（45）

假设一定能到达最后一个位置，计算到达最后一个位置需要使用的最小的步数

容易理解的题解：https://leetcode-cn.com/problems/jump-game-ii/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-10/

简单来说，就是使用贪心思想，有两个变量：end 和 maxPosition

end 表示当前这一步能达到的最远的点，maxPosition 表示下一步能到达的最远的点

```go
func jump2(nums []int) int {
	step, maxPosition, end, length := 0, 0, 0, len(nums)
	for i := 0; i < length-1; i++ {
		maxPosition = int(math.Max(float64(maxPosition), float64(i+nums[i])))
		if i == end {
			step++
			end = maxPosition
		}
	}
	return step
}
```

如例子：{2,3,1,1,4}

当前第0步能到达的最远的点是 0，即end=0，而下一步能到达的最远的点是需要计算的，计算方式 `i+nums[i]`，所以第一步能到达的最远的点是 0+nums[0] = 2，即 maxPosition = 2

当 i 遍历到 end 时，表明这一步已经走到边界点了，这时候需要做两件事

1. 准备开始跳下一步，步数+1
2. end 需要更新，即为 maxPosition

如当前第1步能跳到最远的点是 2，即 end = 2，而下一步能到达的最远的点，需要再这个范围内计算，最后计算得到当 i = 1时，可以取得 maxPosition 的最大值 4
## 3、顺时针翻转（48）
顺时针翻转可以分解为两步：
1. 先对角线翻转
2. 以行的维度上下对换（以中间的一行为轴做轴对称变换）

```go
package leetcode

func rotate(matrix [][]int) {
	length := len(matrix)
	// rotate by diagonal 对角线变换
	for i := 0; i < length; i++ {
		for j := i + 1; j < length; j++ {
			matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
		}
	}
	// rotate by vertical centerline 竖直轴对称翻转
	for i := 0; i < length; i++ {
		for j := 0; j < length/2; j++ {
			matrix[i][j], matrix[i][length-j-1] = matrix[i][length-j-1], matrix[i][j]
		}
	}
}
```
