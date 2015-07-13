## 3Sum

### 题目
Given an array S of n integers, are there elements a,b,c in S such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.  Note:  • Elements in a triplet (a, b, c) must be in non-descending order. (ie, a ≤ b ≤ c)   
• Thesolutionsetmustnotcontainduplicatetriplets. 

``` For example, given array S = {-1 0 1 2 -1 -4}.  A solution set is:  (-1, 0, 1)  (-1, -1, 2)
```

### 思路

```
先对数组进行非递减排序, 确定一个数i，在对其后面的序列使用 左右游标p,q夹逼（PS:这个词确实有点）。  
对num[i],num[p],num[q]三者的和sum 进行判断。   
如果 sum>target: q--; 去重;  
如果 sum<target: p++; 去重;  
如果 sum==target: 返回结果; 去重;  
```

这个算法的时间复杂度为O(n^2).

####去重技巧:

1. 如果num[i] = num[i - 1]，说明刚才i-1时求的解在这次肯定也会求出一样的，所以直接跳过不求；
2. 其实指针p不需要从数组头开始，因为如果num[i]所在的解中如果有i之前的数，设其位置为j，那么我们求num[j]时，肯定把num[i]
    也找出来放到和num[j]一起的解里了，所以指针p其实应该从i+1开始，即初始时p = i + 1, q = num.size() - 1；
3. 当sum == 0，我们保存了当前解以后，需要num[i]在解中的其他的2个数组合，这个时候，肯定是p往后或者q往前，如果++p，发
    现其实num[p] == num[p-1]，说明这个解肯定和刚才重复了，再继续++p。同理，如果--q后发现num[q] == num[q+1]，继续--q。
    这个去重操作主要针对这种有多个同值的数组，如：-3, 1,1,1, 2,2,3,4。

### C++代码

```
#include <iostream>
#include <vector>

using namespace std;

class Solution {
	public:
		vector<vector<int> > threeSum(vector<int> &num) {
			vector<vector<int> > result;
			if(num.size()<3) return result;
			
			sort(num.begin(),num.end());
			printf("size: %d\n", num.size());
			
			for (int i=0; i<num.size(); ++i){
				if(i!=0 && num[i]==num[i-1]) continue;
				
				int p = i+1, q = num.size()-1;
				int sum = 0;
				while(p < q){
					sum = num[i]+num[p]+num[q];
					if(sum<0){
						++p;
						while(num[p]==num[p-1] &&p<q) ++p;
					}else if(sum>0){
						--q;
						while(num[q]==num[q+1] &&p<q) --q;
					}else{
						printf("%d, %d, %d",num[i], num[p], num[q]);
						//result.push_back({num[i], num[p], num[q]});
						vector<int> newRes;  
						newRes.push_back(num[i]);  
						newRes.push_back(num[p]);  
						newRes.push_back(num[q]);    
						result.push_back(newRes); 
						
						while(++p<q && num[p]==num[p-1]){
							//do nothing
						}
						while(--q>p && num[q]==num[q+1]){
							//do nothing
						}
					}
				}
			}
			return result;
		}
};


int main(int argc, char *argv[]) {
	int a[] = {-1,0,1};
	vector<int> v(&a[0],&a[3]);
	Solution s;
	vector<vector<int> > result = s.threeSum(v);
	
}
```