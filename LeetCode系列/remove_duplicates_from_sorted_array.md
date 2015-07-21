### Remove Duplicates from Sorted Array

这里用的是C++, 注意,是Sorted Array!

```
#include <iostream>

using namespace std;

//Remove Duplicates from Sorted Array
class Solution{
	public:
		int removeDuplicates(int arr[], int size) {
			if (size==0) return 0;
			
			int index = 0;
			for(int i=1; i<size; i++) {
				if (arr[index]!=arr[i])
					arr[++index] = arr[i];
			}
			return index + 1;
		}
};



int main(int argc, char *argv[]) {
	int arr[] = {1,1,2};
	Solution solution;
	int length = solution.removeDuplicates(arr, 3);
	printf("%d", length);
}
```

