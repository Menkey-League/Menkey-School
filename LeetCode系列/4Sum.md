
##LeetCode——4Sum & 总结

### 前言

有人对 Leetcode 上 2Sum,3Sum,4Sum,K Sum问题作了总结：
[http://blog.csdn.net/nanjunxiao/article/details/12524405](http://blog.csdn.net/nanjunxiao/article/details/12524405)

对于同类问题做了代码模型：

```
int i = starting; //头指针
int j = num.size() - 1; //尾指针
while(i < j) {
    int sum = num[i] + num[j];
    if(sum == target) {
        store num[i] and num[j] somewhere;
        if(we need only one such pair of numbers)
            break;
        otherwise
            do ++i, --j;
    }
    else if(sum < target)
        ++i;
    else
        --j;
}
```

### 题目

>Given an array S of n integers, are there elements a, b, c, and d in S such that a + b + c + d = target? Find all unique quadruplets in the array which gives the sum of target.Note:  • Elements in a quadruplet (a, b, c, d) must be in non-descending order. (ie, a ≤ b ≤ c ≤ d)   
• Thesolutionsetmustnotcontainduplicatequadruplets.  

> For example, given array S = {1 0 -1 0 -2 2}, and target = 0.A solution set is:    (-1,  0, 0, 1)  (-2, -1, 1, 2)  (-2,  0, 0, 2)


### 思路


关于这道有许多解法：


### 解法一：K Sum 

```
// 先排序,然后左右夹逼,时间复杂度 O(n^3),空间复杂度 O(1) class Solution {public:      vector<vector<int>> fourSum(vector<int>& num, int target) {          vector<vector<int>> result;          if (num.size() < 4) return result;          sort(num.begin(), num.end());          auto last = num.end();          for (auto a = num.begin(); a < prev(last, 3); ++a) {              for (auto b = next(a); b < prev(last, 2); ++b) {                  auto c = next(b);                  auto d = prev(last);                  while (c < d) {                      if (*a + *b + *c + *d < target) {                          ++c;￼                      } else if (*a + *b + *c + *d > target) {                          --d;                      } else {                          result.push_back({ *a, *b, *c, *d });                          ++c;                          --d;} }} }          sort(result.begin(), result.end());          result.erase(unique(result.begin(), result.end()), result.end());          return result;} };
```

补充： 关于unique()去重的使用，  
参考 [http://blog.csdn.net/zlhy_/article/details/8784553](http://blog.csdn.net/zlhy_/article/details/8784553)


### 解法二：Hashmap

用一个 hashmap 先缓存两个数的和, 以及`vector<int, int>`存这两个数。  
在用两个游标遍历序列，key = target -v[x]-v[y], 根据 map.find(key), 找出另外两个数。
时间复杂度,平均 O(n^2),最坏 O(n^4),空间复杂度 O(n^2)

```
class Solution {
	public:
		vector<vector<int> > fourSum(vector<int> &sums, int target) {
			vector<vector<int> > result;
			if (nums.size() < 4) return result;
			sort(nums.begin(), num.end());
			
			unordered_map<int, vector<pair<int, int> > > cache;
			for (int i=0; i<nums.size(); ++i) {
				for (int j=i+1; j<nums.size(); ++j){
					cache[nums[i]+num[j]].push_back(pair<int, int>(i, j));
				}
			}
			
			for (int x=0; x<nums.size(); ++x) {
				for (int y=x+1; y<nums.size(); ++y) {
					int key = target - nums[x] - nums[y];
					if (cache.find(key) == cache.end()) continue;
					
					vector<pair<int, int> > vec = cache[key];
					for (int k=0; k<vec.size(); ++k) {
						if (x <= vec[k].second)
							continue; //有重叠
						result.push_back({ nums[vec[k].first], nums[vec[k].second], nums[c], nums[d]});
					}
				}
			}
			sort(result.begin(), result.end());
			result.erase(unique(result.begin(), result.end()), result.end());
			return result;
		}
};
```

### 解法三：Multimap

首先要说的是 multimap的概念。

multimap提供了可以一种可以有重复键值的STL map类型。其插入方式和map相似，但是由于可以拥有重复键值所以在查找方面有些不同

1. 直接找到每种键值的所有元素的第一个元素的游标

通过函数：lower_bound( const keytype& x ), upper_bound( const keytype& x ) 可以找到比指定键值x的小的键值的第一个元素和比指定键值x大的键值的第一个元素。返回值为该元素的游标。

细节：当到达键值x已经是最大时，upper_bound返回的是这个multimap的end游标。同理，当键值x已经是最小了，lower_bound返回的是这个multimap的begin游标。

2. 指定某个键值，进行遍历

可以使用上面的lower_bound和upper_bound函数进行游历，也可以使用函数equal_range。其返回的是一个游标对。游标对pair::first是由函数lower_bound得到的x的前一个值，游标对pair::second的值是由函数upper_bound得到的x的后一个值。


--------------

这个算法的 时间复杂度 O(n^2),空间复杂度 O(n^2)

```
#include <iostream>
#include <vector>
#include <unordered_multimap>

using namespace std;

class Solution {
	public:
		vector<vector<int> > fourSum(vector<int>& num, int target) {
			vector<vector<int> > result;
			if (num.size()<4) return result;
			sort(num.begin(), num.end());
			
			unordered_multimap<int, pair<int, int>> cache;
			for (int i=0; i+1<num.size(); ++i) {
				for (int j=i+1; j<num.size(); ++j){
					cache.insert(make_pair(num[i]+num[j], make_pair(i, j)));
				}
			}
			
			for (pair i =cache.begin(); i!=cache.end(); ++i){
				int x = target - a->first;
				pair range = cache.equal_range(x);
				for (pair j = range.first; j!=range.second; ++j) {
					int a = i->second.first;
					int b = i->second.second;
					int c = j->second.first;
					int d = j->second.second;
					if (a != c && a != d && b != c && b != d) {
																vector<int> vec = { num[a], num[b], num[c], num[d] };
																sort(vec.begin(), vec.end());
																result.push_back(vec);
					}
				}
			}
			sort(result.begin(), result.end());
			result.erase(unique(result.begin(), result.end()), result.end());
			return result;
		}
};
```
