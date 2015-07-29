# Same Tree & Symmetric Tree

### Same Tree

判断两个二叉树是否相等，包括结构相等和节点值相等。

C++代码
第一种：

```
// 递归版,时间复杂度 O(n),空间复杂度 O(logn) 
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if(!p&&!q) return true;
        if(!p||!q) return false;
        return p->val==q->val
            && isSameTree(p->left,q->left)
            && isSameTree(p->right,q->right);
    }
};
```

第二种：

```
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        stack<TreeNode *>s;
        s.push(p);
        s.push(q);
        
        while(!s.empty()) {
            q = s.top(); s.pop();
            p = s.top(); s.pop();
            
            if(!p&&!q) continue;
            if(!p||!q) return false;
            if(p->val != q->val) return false;
            
            s.push(p->left);
            s.push(q->left);
            
            s.push(p->right);
            s.push(q->right);
        }
        return true;
    }
};
```

### Symmetric Tree 

Symmetric Tree 与 Same Tree类似，判断是否为对称二叉树。
算法 左右节点交换对比。

For example, this binary tree is symmetric:

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

But the following is not:

```
    1
   / \
  2   2
   \   \
   3    3
```

C++代码
第一种：
```
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        return root ?isSymmetric(root->left, root->right) : true;
    }
    
    bool isSymmetric(TreeNode *left, TreeNode *right){
        if (!left && !right) return true;
        if (!left || !right) return false;
        return left->val==right->val
            && isSymmetric(left->left, right->right)
            && isSymmetric(left->right, right->left);
    }
};
```

第二种：
```
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(!root) return true;
        
        stack<TreeNode *>s;
        s.push(root->left);
        s.push(root->right);
        
        while(!s.empty()){
            TreeNode *p = s.top(); s.pop();
            TreeNode *q = s.top(); s.pop();
            
            if(!p&&!q) continue;
            if(!p||!q) return false;
            if(p->val!=q->val) return false;
            
            s.push(p->left);
            s.push(q->right);
            
            s.push(p->right);
            s.push(q->left);
        }
        return true;
    }
    
};
```