# LeetCode-- Binary Tree Traversal


> ### Binary Tree Preorder Traversal

> ### Binary Tree Inorder Traversal

> ### Binary Tree Postorder Traversal

LeetCode 上二叉树的节点定义如下:  

```// 树的节点 struct TreeNode {      int val;      TreeNode *left;      TreeNode *right;      TreeNode(int x) : val(x), left(nullptr), right(nullptr) { }};
```

常规递归算法

```
/**
 * 递归先序遍历
 */
void preOrder_traverse_recur(BiTree T) {
  if(T == NULL) {
    return;
  } else {
    printf("%d", T->val);
    preOrder_traverse_recur(T->left);
    preOrder_traverse_recur(T->right);
  }
}

/**
 * 递归中序遍历
 */
void inOrder_traverse_recur(BiTree T) {
  if(T == NULL) {
    return;
  } else {
    inOrder_traverse_recur(T->left);
    printf("%d", T->val);
    inOrder_traverse_recur(T->right);
  }
}

/**
 * 递归后序遍历
 */
void postOrder_traverse_recur(BiTree T) {
  if(T == NULL) {
    return;
  } else {
    postOrder_traverse_recur(T->left);
    postOrder_traverse_recur(T->right);
    printf("%d", T->val);
  }
}
```

## 栈 算法

时间复杂度 O(n),空间复杂度 O(n)

```// LeetCode, Binary Tree Preorder Traversal 
class Solution {public:      vector<int> preorderTraversal(TreeNode *root) {          vector<int> result;          const TreeNode *p;          stack<const TreeNode *> s;          p = root;          if (p != nullptr) s.push(p);          while (!s.empty()) {              p = s.top();              s.pop();              result.push_back(p->val);              if (p->right != nullptr) s.push(p->right);              if (p->left != nullptr) s.push(p->left);          }          return result;      }};

```

```
// LeetCode, Binary Tree Inorder Traversal 
class Solution {public:      vector<int> inorderTraversal(TreeNode *root) {          vector<int> result;          const TreeNode *p = root;          stack<const TreeNode *> s;          while (!s.empty() || p != nullptr) {              if (p != nullptr) {s.push(p);                  p = p->left;              } else {                  p = s.top();                  s.pop();                  result.push_back(p->val);                  p = p->right;} }          return result;      }};
```


```
// LeetCode, Binary Tree Postorder Traversal
class Solution {public:vector<int> postorderTraversal(TreeNode *root) { vector<int> result;/* p,正在访问的结点,q,刚刚访问过的结点 */ const TreeNode *p, *q;          stack<const TreeNode *> s;￼
5.1 二叉树的遍历 91￼p = root;do {while (p != nullptr) { /* 往左下走 */s.push(p);p = p->left; }        q = nullptr;        while (!s.empty()) {p = s.top();s.pop();/* 右孩子不存在或已被访问,访问之 */ if (p->right == q) {                result.push_back(p->val);q = p; /* 保存刚访问过的结点 */ } else {/* 当前结点不能访问,需第二次进栈 */ s.push(p);/* 先处理右子树 */p = p->right;break; }        }    } while (!s.empty());    return result;}};
```

## Morris算法

时间复杂度 O(n),空间复杂度 O(1)

参考：[http://www.tuicool.com/articles/zA7NJbj](http://www.tuicool.com/articles/zA7NJbj)

算法伪码（Morris InOrder) :

```
while 节点非空
   if 当前节点没有左子树
     访问该节点
     转向右节点
   else
     找到左子树的最右节点
     	if 最右子节点指空
     		最右节点的右指针指向根
     		转向左子树节点
     	else //最右子节点指根
     		消除指根指针
     		根节点右移

```

![bitree_mirros](./img/leetcode_bitree_morris.jpg)


C++实现：

```
Morris 中序遍历

void morris_inorder(TreeNode *root) {  
   TreeNode *p = root, *tmp;
   while (p) {  
       if (p->left == NULL) {  
           printf("%d ", p->key);  
           p = p->right;  
       }  
       else {  
           tmp = p->left;  
           while (tmp->right != NULL && tmp->right != p)  
               tmp = tmp->right;  
           if (tmp->right == NULL) {  
               tmp->right = p;  
               p = p->left;  
           } else {  
               printf("%d ", p->key);  
               tmp->right = NULL;  
               p = p->right;  
           }  
       }  
   }  
}
```

```
Morris 先序遍历

void morris_preorder(TreeNode *root) {
	TreeNode *p = root, *tmp;
	while (p) {
		if (p->left == NULL) {
			printf("%d ",p->key);
			p = p->right;
		} else {
			tmp = p->left;
			while (tmp->right != NULL && tmp->right != p) {
				tmp = tmp->right;
			}
			if (tmp->right == NULL){
				print("%d ",p->key);
				tmp->right = p;
				p = p->left;
			} else {
				tmp->right = NULL;
				p = p->right;
			}
		}
	}
}

```


Morris后序遍历二叉树的算法与上面的算法思想一致，只是在遍历前，增加了一个类似头节点的节点作为整个遍历过程的起始节点。

```
/**
 * morris后序遍历算法 
 */
void morris_postOrder(BiTree T) {
  BNode *dump = malloc(sizeof(BNode));
  BNode *p, *temp;
  dump->left = T;
  p = dump;
  while(p) {
    if(p->left == NULL) {
      p = p->right;
    } else {
      temp = p->left;
      while(temp->right != NULL && temp->right != p) {
        temp = temp->right;
      }
      if(temp->right == NULL) {
        temp->right = p;
        p = p->left;
      } else {
        printReverse(p->left, temp);
        temp->right = NULL;
        p = p->right;
      }
    }
  }
  free(dump);
}


```

代码中的printReverse()函数就是逆序遍历从p->left到temp这条路径上的节点的过程;  
printReverse函数先将从from节点到to节点的这条路径反转，再输出，最后还原.

```
/**
 * 相当于单链表的反转
 */
void reverse(BNode *from, BNode *to) {
  BNode *x, *y, *z;
  if(from == to) {
    return;
  }
  x = from;
  y = from->right;
  while(x != to) {
    z = y->right;
    y->right = x;
    x = y;
    y = z;
  }
}

void printReverse(BNode *from , BNode *to) {
  BNode *p;
  reverse(from, to);
  p = to;
  while(1) {
    printf("%4c", p->ch);
    if(p == from) {
      break;
    }
    p = p->right;
  }
  reverse(to, from);
}
```














