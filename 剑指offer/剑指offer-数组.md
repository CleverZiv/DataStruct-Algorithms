---
title:数组面试题
date: 2018-04-30 14:13:26
tags: [剑指offer]
categories: [学习,编程]
---
###剑指offer题目分类
#####1、数组
面试题4：二维数组的查找
```java
public class Solution4{
	public boolean Find(int[] array, int target){
    	if(array == null || array.length <= 0) return false;
        int row = array.length; int col = array[0].length;
        int i = 0; int j = col - 1;
        while(i < row && j >= 0){
        	if(target > array[i][j]) i++;
            else if(target < array[i][j]) j--;
            else return true;
        }
        return false;
    }
}
```

面试题21：调整数组顺序使得奇数位于偶数前面
思路一：如果可以使用额外的空间，那就可以实现空间复杂度为O(n)，时间复杂度为O(n)的解法
```java
public class Solution21{
	public int[] reOrderArray1(int[] array){
    	if(array = null || array.length <= 0) return array;
        //重新申请一个同样长度的array
        int[] A = new int[array.length];
        //遍历array得到奇数和偶数的个数
        int ji = 0, ou = 0;
        for(int i = 0; i < array.length; i++){
        	if((array[i] % 2) != 0) ji++;
            else ou++;
        }
        //再次遍历数组，将奇数和偶数放入A中
        int indexji = 0, indexou = ji+1;
        for(int i = 0; i < A.length; i++){
        	if((array[i] % 2) != 0) A[indexji++] = array[i];
            else A[indexou++] = array[i];
        }
        return A;
    }
}
```

思路二：若无法使用额外的空间，使用插入排序的思想，从左至右遍历数组，遇到奇数且前一位是偶数，则将奇数前移(交换自己的前一位)，并继续判断交换后所在的位置的前一位是否为偶数，是的话继续前移。
如：(1,2,4,3) -> (1,2,3,4) -> (1,3,2,4)
这保证了相对位置不变。
```java
public class Solution21{
	public void reOrderArray2(int[] array){
    	if(array == null || array.length <= 0) return;
        for(int i = 0; i < array.length; i++){
        	if((array[i] % 2) != 0) {
            	//此数为奇数，则判断其前一个数是否为偶数
                int index = i;
                while(index-1 >= 0 && (array[index-1] % 2) == 0){
                    //while语句中必须先检查数组越界
                	//交换
                    int temp = A[index-1];
                    A[index-1] = A[index];
                    A[index] = temp;
                    index--;
                }
            }
        }
    }
}
```
面试题39：数组中出现超过一半的数字
思路一：若可以使用额外的空间，使用HashMap保存遍历数组得到的每个数的个数，然后再遍历HashMap得到大于一半的数即可。时间复杂度为O(n)，空间复杂度为O(n).
```java
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
public class Solution39 {
    public int MoreThanHalfNum_Solution(int [] array) {
        //1、遍历数组，采用hashmap存储对应的值和数量，最后从hashmap中取出value超过一半的数对应的key
        int res = 0;
        if(array == null || array.length == 0) return res;
        HashMap<Integer,Integer> map = new HashMap<Integer,Integer>();
        //2、遍历array
        for(int i = 0; i < array.length; i++){
            map.put(array[i], map.getOrDefault(array[i], 0) + 1);
        }
        //3、遍历hashmap，找到value > array.length/2的数
        Iterator iter = map.entrySet().iterator();
        while(iter.hasNext()){
            Map.Entry entry = (Map.Entry)iter.next();
            Integer val = (Integer)entry.getValue();
            if(val > array.length / 2){
                res = (Integer)entry.getKey();
                break; //找到就跳出循环
            } 
                
        }
        return res;
    }
}
```
思路二：不能使用额外的空间。试想一下，假设将原数组排序，如果数组中存在一个数，它的个数超过了一半，那么它就一定位于数组的中位数位置。运用快速排序的划分方法(划分方法每次得到的是该数在整个排序数组中的下标index)，得到该中位数。注意，此时还要判断该中位数是否出现个数超过了一半。(中位数只是它的必要不充分条件)。
```java
package com.newCode;

public class Solution39 {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		int[] A = {1,2,3,2,2,2,5,4,2};
		int res = MoreThanHalfNum_Solution(A);
		System.out.println(res);
	}
	public static int MoreThanHalfNum_Solution(int [] array){
    	if(array == null || array.length <= 0) return 0;
        int index = array.length / 2;
        int start = 0; int end = array.length-1;
        int k = partition(array, start, end);
        while(k != index){ //在此处卡了一下
        	if(k > index) {end = k -1; k = partition(array, start, end);}
            else {start = k+1; k = partition(array, start, end);}
        }
        //此时k为中位数的下标
        int target = array[k];
        //计算target出现的次数，遍历数组
        int count = 0;
        for(int i = 0; i < array.length; i++){
        	if(array[i] == target) count++;
        }
        return (count > (array.length / 2) ? target : 0);
    }
    //快速排序的核心，partition方法
     private static int partition(int[] A, int start, int end){
    	int mid = (start + end) / 2;
        while(start <= end){
            while(A[start] < A[mid]) start++;
            while(A[end] > A[mid]) end--;
            if(start <= end) {
                swap(A, start, end);
                start++; end--;
            }
        }
            return start;
        }
    //辅助方法
    private static void swap(int[] A, int i , int j){
    	int temp = A[i];
        A[i] = A[j];
        A[j] = temp;
    }

}
```
该方法的时间复杂度为O(n)

面试题40：最小的k个数
思路一：作为对上题的一个复习，快速排序的思想，上题是找中位数，这一题是找第k个数，比它小的都在左边，这样的话就可以找出最小的k个数。
```java
public class Solution40{
	public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k){
    	ArrayList<Integer> res = new ArrayList<Integer>();
    	if(input == null || input.length <= 0) return res;
        int start = 0, end = input.length-1;
        int index = partition(input, start, end);
        while(index != k){
        	if(index > k) {
            	end = index - 1;
                index = partition(input, satrt, end);
            }else {
            	start = index + 1;
                index = partition(input, start, end);
            }
        }
        //得到了index,此时的input数组的前k个数即为最小数
        for(int i = 0; i < k; i++){
        	res.add(input[i]);
        }
        return res;
    }
    //辅助函数partition
    private int partition(int[] A, int start, int end){
    	int pivotValue = A[start];
        int pivotKey = start;
        while(start < end){
    		while(start < end && A[start] <= pivotValue) start++;
        	while(start < end && A[end] >= pivotValue]) end--;
        	swap(A, start, end);
    	}
    	swap(A, pivotKey, start);
    	return start;
    }
}
```

****以后快速排序都用这种写法，其他的东西忘记
```java
public static int partition(int[] A, int start, int end){
	int pivotValue = A[start];
    int pivotKey = start;
    while(start < end){
    	while(start < end && A[start] <= pivotValue) start++;
        while(start < end && A[end] >= pivotValue]) end--;
        swap(A, start, end);
    }
    swap(A, pivotKey, start);
    return start;
}
```
完整的快速排序
```java
package com.aze;

import java.util.*;  
//快速排序，使用分治思想，通过递归来分割地解决问题，关键是返回分界之后分界点的位置，以便进行下一次的递归分界  

public class QuickSort {  
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		int[] A = {4,5,1,6,2,7,8,3,11,47,7,65};
		int[] B = new int[A.length];
		B = quickSort(A,A.length);
		for(int i = 0; i < B.length; i++){
			System.out.println(B[i]);
		}
	}
    public static int[] quickSort(int[] A, int n) {  
     //特殊输入  
     if(A==null||A.length<=0) return A;  
     //调用一个递归的quick()方法来实现快速排序  
     quick(A,0,A.length-1);  
     return A;  
 }  
 //写一个递归方法quick()通过递归调用自己来不断分割给定的区间,假设执行quick(array,start,end)方法后数组就完成排序  
 public static void quick(int[] array,int start,int end){  
     //递归方法一定要有递归结束的边界条件,本题结束的边界条件是要分割的区域只有一个元素  
     if(start>=end) return;  
     //调用partition()方法来对[start,end]范围的数组进行分界，并返回分界元素的位置下标  
     int middle=partition(array,start,end);  
     //递归调用divide()方法对已经得到的2个子数组进行分界，假设调用divide()方法后数组就可以对该范围完成分界  
//if (middle > start + 1) {不需要写，递归终止条件已经可以终止递归  
           quick(array, start, middle-1);  
       //}  
       //if (middle<end) {不需要写，递归终止条件已经可以终止递归  
           quick(array, middle, end);  
       //}  
 }  
 //核心方法partition()，用来对[start,end]范围的数组进行分界并且返回分界值的新下标  
 public static int partition(int[] A,int start,int end){  
	int pivotValue = A[start];
    int pivotKey = start;
    while(start < end){
    	while(start < end && A[start] <= pivotValue) start++;
        while(start < end && A[end] >= pivotValue]) end--;
        swap(A, start, end);
    }
    swap(A, pivotKey, start);
    return start;
 }
 //辅助函数，专门用来交换数组中的2个元素  
 public static void swap(int[] array,int p1,int p2){  
     int temp=array[p1];  
     array[p1]=array[p2];  
     array[p2]=temp;  
 }  
}
```
不写不知道，一写发现之前认为的快速排序的代码是有问题的。问题出在，它并没有实现对等于基准数的处理，尽管最后处理了，但是在每一步并没有很好的处理。对于核心函数partition函数，记住其在哪个地方使用“<”,那个地方使用"<="

思路二：维护大小为k的最大堆。
```java
public class Solution40 {
    public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
        //特殊输入
        ArrayList<Integer> res = new ArrayList<Integer>();
        if(input == null || input.length < k || k<=0) return res;
        //对数组的前k个数进行最大堆的构造
        for(int i = k/2; i >= 0; i--){
        	adjustHeap(input, i, k-1);
        }
        //将后续的数一个一个与k大小的堆中的最大值比较
        for(int j = k; j < input.length; j++){
        	if(input[j] > input[0]) continue;
            else{
            	int temp = input[0];
                input[0] = input[j];
                input[j] = temp;
                adjustHeap(input, 0, k-1);
                }
    	}
        //遍历前k个数
        for(int i = 0; i < k; i++){
        	res.add(input[i]);
        }
        return res;
    }
    //辅助函数adjustHeap
    private void adjustHeap(int[] A, int s, int k){
    	int temp = A[s];
        int i = s;
        	for(int i = 2*s+1; i < k; i = 2*i+1){
            	if((i != k-1) && A[i+1] > A[i]) i++;
                if(temp > A[i]) break;
                else{
                 A[s] = A[i];
                 s = i;
                }
            }
        A[i] = temp;
    }
}
```

面试题45：把数组排成最小的数
1、利用重写集合的comparator方法，建立一个新的比较规则
2、将整型数组转换成String，一方面规避溢出的问题，另一方面符合题目输出要求
```java
import java.util.ArrayList;

public class Solution45 {
    public String PrintMinNumber(int [] numbers) {
		String s = "";
        if(numbers == null || numbers.length <= 0) return s;
        //将数组中的数放入list中
        ArrayList<Integer> list = new ArrayList<Integer>();
        for(int i = 0; i < numbers.length; i++){
        	list.add(numbers[i]);
        }
        //将list排序，调用collections包装类中的sort方法
        
        Collections.sort(list,new Comparator<Integer>(){
        	public int compare(Integer p, Integer q){
            	Sting s1 = p + "" + q;
                String s2 = q + "" + p;
                return s1.compareTo(s2);
            }
        });
        //此时list已经排好序
        for(int l : list){
        	s += l;
        }
        return s;
    }
}
```

面试题51：数组中的逆序对
经过分析，这道题是一个归并排序的过程
```java
public class Solution51 {
	int cnt;
    public int InversePairs(int [] array) {
        cnt = 0;
         if(array != null)
            mergeSortUp2Down(array,0,array.length - 1);
        return cnt;
    }
    //归并排序的divide方法
    public void divide(int[] array, int start, int end){
    	if(start >= end) return;
        int mid = (start + end) / 2;
        divide(array, start, mid);
        divide(array, mid+1, end);
        //再组合起来merge方法
    }
    
    public void merge(int[] array, int start, int end){
    	int mid = (start + end) / 2;
        int[] temp = new int[array.length];
        int i = start, j = mid + 1, k = start;
        while(i <= mid && j <= end){
        	if(array[i] < array[j]) temp[k++] = array[i++];
            else{
            	temp[k++] = array[i++];
                cnt += mid-i+1;
                cnt %= 1000000007;
            }
        }
        while(i <= mid){
        	temp[k++] = array[i++];
        }
        while(j <= end){
        	temp[k++] = array[j++];
        }
        for(k = start; k < end; k++){
        	array[k] = temp[k];
        }
    }
}
```

面试题53-1：数字在排序数组中出现的次数
```java
public class Solution53 {
    public int GetNumberOfK(int [] array , int k) {
       return binarySearch(array, k + 0.5) - binarySearch(array, k-0.5);
    }
    //写一个返回某个数应该插入数组中哪个位置的二分查找
    public int binarySearch(int[] A, double target){
        int lo = 0; int hi = A.length - 1;
        while(lo <= hi){
            int mid = (lo + hi) / 2;
            if(A[mid] < target) lo = mid + 1;
            else if(A[mid] > target) hi = mid - 1;
           // else return mid;
        }
        return lo;
    }
}
```

面试题3：数组中重复的数字
```java
public class Solution3 {
    // Parameters:
    //    numbers:     an array of integers
    //    length:      the length of array numbers
    //    duplication: (Output) the duplicated number in the array number,length of duplication array is 1,so using duplication[0] = ? in implementation;
    //                  Here duplication like pointor in C/C++, duplication[0] equal *duplication in C/C++
    //    这里要特别注意~返回任意重复的一个，赋值duplication[0]
    // Return value:       true if the input is valid, and there are some duplications in the array number
    //                     otherwise false
    public boolean duplicate(int numbers[],int length,int [] duplication) {
        if(numbers == null || numbers.length <= 0) return false;
        for(int i = 0; i < numbers.length; i++){
            while(numbers[i] != i){
                if(numbers[i] == numbers[numbers[i]]){
                    duplication[0] = numbers[i];
                    return true;
                }
                int temp = numbers[i];
                numbers[i] = numbers[numbers[i]];
                numbers[temp] = temp;
            }
        }
        return false;
    }
}
```

面试题66：构建乘积数组
```java
import java.util.ArrayList;
public class Solution {
    public int[] multiply(int[] A) {
		int length = A.length;
        int[] B = new int[length];
        if(length != 0){
        	B[0] = 1;
            //计算下三角连乘
            for(int i = 1; i < length; i++){
            	B[i] = B[i-1] * A[i-1];
            }
            int temp = 1;
            //计算上三角
            for(int j = length - 2; j >= 0; j--){
            	temp *= A[j+1];
                B[j] *= temp;
            }
            return B;
        }
    }
}
```































