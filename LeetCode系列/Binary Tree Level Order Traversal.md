# Binary Tree Level Order Traversal


这几个题目类似，都是用相同的递归法,时间空间复杂度都为O（n).

当然，还有迭代法，控件复杂度会降至 O(1），这个暂不讨论。


## Binary Tree Level Order Traversal

```
// 递归版,时间复杂度 O(n),空间复杂度 O(n) class Solution {public:      vector<vector<int> > levelOrder(TreeNode *root) {          vector<vector<int>> result;          traverse(root, 1, result);          return result;}      void traverse(TreeNode *root, size_t level, vector<vector<int>> &result) {          if (!root) return;          if (level > result.size())              result.push_back(vector<int>());          result[level-1].push_back(root->val);          traverse(root->left, level+1, result);          traverse(root->right, level+1, result);} };
```


## Binary Tree Level Order Traversal II

```
// 递归版,时间复杂度 O(n),空间复杂度 O(n)class Solution {public:vector<vector<int> > levelOrderBottom(TreeNode *root) { vector<vector<int>> result;traverse(root, 1, result);std::reverse(result.begin(), result.end()); // 比上一题多此一行 return result;}      void traverse(TreeNode *root, size_t level, vector<vector<int>> &result) {          if (!root) return;          if (level > result.size())              result.push_back(vector<int>());          result[level-1].push_back(root->val);          traverse(root->left, level+1, result);          traverse(root->right, level+1, result);} };
```


## Binary Tree Zigzag Level Order Traversal

```
// 递归版,时间复杂度 O(n),空间复杂度 O(n)class Solution {public:      vector<vector<int> > zigzagLevelOrder(TreeNode *root) {          vector<vector<int>> result;          traverse(root, 1, result, true);          return result;}      void traverse(TreeNode *root, size_t level, vector<vector<int>> &result,              bool left_to_right) {          if (!root) return;          if (level > result.size())              result.push_back(vector<int>());          if (left_to_right)              result[level-1].push_back(root->val);          else              result[level-1].insert(result[level-1].begin(), root->val);          traverse(root->left, level+1, result, !left_to_right);          traverse(root->right, level+1, result, !left_to_right);      }};
```