## Longest Consecutive Sequence


### 补充一些map的用法

begin，end，rbegin，rend，empty，clear，size，max_size 八个常用的函数.

map.begin(); 是map的起始位置

map.end(); 是指map的尾部，没有实际元素.

map.find(); 查找函数

map.rebgin()和.rend()是反向遍历数据的起始位置和终止位置

map.empty() 判断map是否为空

map.clear() 清空map

map.size() 返回元素的数目

map.max_size()  只是一个容器的大小限定。  
决于 key Value所占字节比较大的一个。然后用4个字节的数字（unsigned_int_max=40亿左右） 除以2除以 所占字节就是这个值了。

另外：  

unordered_map 的元素不以键值或映射的元素作任何特定的顺序排序    
unordered_map 容器比map容器更快地通过键值访问他们的单个元素

插入：

插入有三种方式，第一种：insert（pair<T1,T2,>(key1,value1)）。第二种：insert(map<T1,T2>::value_type(key1,value1)）,这种插入方式和第一种基本相似。第三种：利用数组进行插入，这个一会用程序演示吧。

删除：

有三种方式进行删除，第一种：erase（map<T1,T2>::iterator iter），删除迭代器所指的节点。第二种：erase（key k），根据键值进行删除，删除键值k所指的节点 。第三种：erase（map<T1,T2>::iteratormap iter1，<T1,T2>::iteratoriter2），删除iter1和iter2之间的数据

### iterator:

```
map<int,string> _map;
map<int,string>::iterator iter;
for(iter = _map.begin() ; iter != _map.end() ; ++iter)
{
  cout<<iter->first<<" "<<iter->second<<endl;
}
```


### 题解 Longest Consecutive Sequence
#### 题目：
Given an unsorted array of integers, find the length of the longest consecutive elements sequence.For example, Given [100, 4, 200, 1, 3, 2], The longest consecutive elements sequence is [1, 2, 3, 4]. Return its length: 4.Your algorithm should run in O(n) complexity.

#### 思路：

用一个哈希表 unordered_map<int, bool> used 记录每个元素是否使用,对每个元素,以该元素为中心,往左右扩张,直到不连续为止,记录下最长的长度。

时间复杂度 O(n),空间复杂度 O(n)

#### C++代码：

```
#include <iostream>
#include <vector>
#include <unordered_map>

using namespace std;

class Solution{
	public:
		int longestConsecutive(const vector<int> &num) {
			unordered_map<int, bool> used;
			
			for (int i:num) used[i] = false;
			
			int longest = 0;
			
			for (int i:num) {
				if (used[i]) continue; //结束本次循环
				
				int length = 1;
				
				used[i] = true;
				
				for (int j = i+1; used.find(j)!=used.end(); ++j){
					used[j] = true;
					++length;
				}
				
				for (int j = i-1; used.find(j)!=used.end(); --j){
					used[j] = true;
					++length;
				}
				longest = max(longest, length);
			}
			return longest;
		}
};

int main(int argc, char *argv[]) {
	int arr[] = {100,4,200,1,3,2};
	vector<int> vec(&arr[0],&arr[6]);
	Solution s;
	int result = s.longestConsecutive(vec);
	printf("%d",result);
}
```

