Search in Rotated Sorted Array II

思路：
如同`I`一样，边界判断是非常重要的,注意`<=``>=`。
因为本题是可重复元素，所以 nums[left]<=nums[mid]无法判断为递增。
需要多一个条件判断，跳过重复的元素。


```
bool search(int* nums, int numsSize, int target) {
    if(numsSize==0) return false;
    int left=0;
    int right= numsSize-1;
    do{
        int mid = (left+right)/2;
        if(nums[mid]==target) return true;
        
        if(nums[left]<nums[mid]){
            if(nums[left]<=target && target<nums[mid]){
                right = mid-1;
            }else{
                left = mid+1;
            }
        } else if(nums[left]>nums[mid]){
            if(nums[mid]<target && target<=nums[right]){
                left = mid+1;
            }else{
                right = mid-1;
            }
        } else {
            left++;
        }
    }while(left<=right);
    return false;
}
```