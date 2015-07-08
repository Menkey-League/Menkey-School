Median of Two Sorted Arrays

这道题要找Median,中位数。这个是指，如果数组大小是偶数，返回中间两个数的平均值，如果是奇数个，就是中间的数。
算法时间效率要求是 O(log(m + n)),具体思路网上都一样。


另外，现在leetCode的C++ 数组都换成vector了，所以只好整理一下vector的用法。
参考：[http://imatlab.lofter.com/post/286ffc_a81276]()  
[http://www.cnblogs.com/wang7/archive/2012/04/27/2474138.html](http://www.cnblogs.com/wang7/archive/2012/04/27/2474138.html)


C代码

```
#include <iostream>

using namespace std;

double findKth(int A[], int m, int B[], int n, int k){
	if ( m > n) return findKth(B, n, A, m, k);
	if ( m == 0) return B[k-1];
	if (k <= 1) return A[0]<B[0]?A[0]:B[0];
	
	int ia = (k/2 < m)?k/2:m, ib = k-ia;
	if (A[ia-1] < B[ib-1]){
		return findKth(A+ia, m-ia, B, n, k-ia);
	} else if (A[ia-1] > B[ib-1]){
		return findKth(A, m, B+ib, n-ib, k-ib);
	} else {
		return A[ia-1];
	}
}

double findMedianSortedArrays(int* nums1, int nums1Size, int* nums2, int nums2Size) {
	int k = nums1Size + nums2Size;
	if (k & 0x1){
		return findKth(nums1, nums1Size, nums2, nums2Size, k/2 +1);
	} else {
		return (findKth(nums1, nums2Size, nums2, nums2Size, k/2) + findKth(nums1, nums1Size, nums2, nums2Size, k/2 +1)/2);
	}
}


int main(int argc, char *argv[]) {
	int a[] = {1, 3, 5, 7, 9};
	int b[]	= {2, 4, 6, 8};
	
	double result = findMedianSortedArrays(a, 5, b, 4);
	
	printf("%d", (int)result);
}
```


C++代码

```
include <iostream>
#include <vector>

using namespace std;

class Solution {
	public:
		double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
			int m = nums1.size();
			int n = nums2.size();
			int total = m + n;
			if (total & 0x1)
				return find_kth(nums1, nums2, total/2+1);
			else {
				return (find_kth(nums1, nums2, total/2) + find_kth(nums1, nums2, total/2+1))/2.0;
			}
		}
	private:
		int find_kth(vector<int>& A, vector<int>& B, int k) {
			int m = A.size();
			int n = B.size();
			if (m > n) return find_kth(B, A, k);
			if (m == 0) return B[k-1];
			if (k == 1) return min(A[0], B[0]); //min(a,b)和max(a,b)都是可用的
			
			int ia = min(k/2, m), ib = k-ia;
			if (A[ia-1] < B[ib-1]){
				A.erase(A.begin(), A.begin()+ia);
				return find_kth(A, B, k-ia);
			}else if (A[ia-1] > B[ib-1]){
				B.erase(B.begin(), B.begin()+ib);
				return find_kth(A, B, k-ib);
			}else {
				return A[ia-1];
			}
		}
};

int main(int argc, char *argv[]) {
	int a[] = {1, 3, 5, 7, 9};
	int b[]	= {2, 4, 6, 8};
	
	vector<int> A (&a[0], &a[5]); // copy 5 elements
	vector<int> B (&b[0], &b[4]); // copy 4 elements
	
	Solution solution;
	double result = solution.findMedianSortedArrays(A, B);
	
	printf("%d", (int)result);
}

```