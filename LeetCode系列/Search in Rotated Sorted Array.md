### Search in Rotated Sorted Array


#### 前言：
这道题你在网上搜，大多数都是错的。
下面这样：
使用while(first!=last)这样循环，
最后返回-1.
这样做 first==last时，就会跳出循环，从而出错。
正确的做法应该是 do{}while(first!=last).

#### 思路：

数组是这样的： 大------小-----中

边界分为四种情况：  
left<mid: key是否在(left,mid)中，  
			如果在，  right = mid-1;  
			如果不在， left = mid+1;   
			
left>mid: key是否在(mid,right)中，  
			如果在，  left = mid+1;  
		  	如果不在， right = mid-1;
 

#### 效率：
时间复杂度 O(log n),空间复杂度 O(1)



#### C代码

```
#include <iostream>

using namespace std;

int search(int* nums, int numsSize, int target) {
	int left = 0;
	int right = numsSize-1;
	do{
		int mid = (left+right)/2;
		if(nums[mid]==target) return mid;
	
		if(nums[left]<=nums[mid]){
			if(nums[left]<=target && target<nums[mid]){
				right = mid-1;
			}else{
				left = mid+1;
			}
		} else {
			if(nums[mid]<target && target<=nums[right]){
				left = mid+1;
			}else{
				right = mid-1;
			}
		}
	}while(left<=right);		
	return -1;
}

int main(int argc, char *argv[]) {
	int arr[] = {3,1,2};
	int result = search(arr,3,2);
	printf("%d", result);
	return 0;
}
```
