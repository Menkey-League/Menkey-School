
Remove Duplicates from Sorted Array II 

我开始时写的是这样：

```
int removeDuplicates(int* nums, int numsSize) {
    if(numsSize==0) return 0;
    int index=0, times=0;
    for(int i=1; i<numsSize; i++){
        if(nums[i]!=nums[index]){
            times=0;
            nums[++index]=nums[i];
        }else{
            times++;
            if(times==1){
                nums[++index]=nums[i];
            }else{
                //do nothing
            }
        }
    }
    return index+1;
}

```

其实可以更简洁点：

```
class Solution {
public:
int removeDuplicates(int A[], int n) {
if (n <= 2) return n;
int index = 2;
for (int i = 2; i < n; i++){
if (A[i] != A[index - 2])
A[index++] = A[i];
}
return index;
}
};
```