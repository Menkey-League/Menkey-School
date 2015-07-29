## Recover Binary Search Tree 

>Two elements of a binary search tree (BST) are swapped by mistake.

>Recover the tree without changing its structure.

>Note:
>A solution using O(n) space is pretty straight forward. Could you devise a constant space solution?


这道题要修正二叉查找树错误节点，不改变结构。
并且时间复杂度药O(n)，空间复杂度药O(1).
这道题在LeetCode上的标注是 Hard.

思路：

Here's an example:

```
   1
  / \
 2   3
    /
   4
    \
     5
```

The above binary tree is serialized as "{1,2,3,#,#,4,#,#,5}".
题意二叉查找树不合法，有两个节点的值被交换了，找出这两个节点并且不改变树的结构，使得二叉查找树合法，常数空间限制。
 
这题的要点就是想到使用树的递归中序遍历，因为二叉查找树合法的情况，中序遍历的值是从小到大排列的。
 
当出现当前值比前一个值小的时候，就是存在不合法的节点。
 
用pre存中序遍历时当前节点的前一个节点，方便值的大小对比，用s1，s2记录这两个不合法序列的位置，s1存较大的值，s2存较小的值。
 
最后把两个不合法的值交换。


C++代码：

这里贴一个简易版的：

```
 TreeNode *s1,*s2,*pre;  
    void traversal(TreeNode *root)  
    {  
        if(!root)return ;  
        traversal(root->left);  
        if(pre&& pre->val > root->val)  
        {  
            if(s1==NULL)s1=pre,s2=root;  
            else s2=root;  
        }  
        pre=root;  
        traversal(root->right);  
    }  
    void recoverTree(TreeNode *root) {  
        if(!root)return ;  
        s1=s2=pre=NULL;  
        hehe(root);  
        swap(s1->val,s2->val);  
    } 
```


最后我直接贴个牛人写的代码：

```
void recoverTree(TreeNode *root)
{     
       TreeNode *f1=NULL, *f2=NULL;
       TreeNode  *current,*pre, *parent=NULL;

       if(root == NULL)
             return;
       bool found = false;
       current = root;
       while(current != NULL)
       {                
             if(current->left == NULL)
             {
                    if(parent && parent->val > current->val)
                    {
                           if(!found)
                           {
                                 f1 = parent;
                                 found = true;
                           }
                           f2 = current;
                    }
                    parent = current;
                    current = current->right;     
             }   
             else
             {
                    /* Find the inorder predecessor of current */
                    pre = current->left;
                    while(pre->right != NULL && pre->right != current)
                           pre = pre->right;

                    /* Make current as right child of its inorder predecessor */
                    if(pre->right == NULL)
                    {
                           pre->right = current;
                           current = current->left;
                    }

                    /* Revert the changes made in if part to restore the original
                    tree i.e., fix the right child of predecssor */  
                    else
                    {
                           pre->right = NULL;
                           if(parent->val > current->val)
                           {
                                 if(!found)
                                 {
                                        f1 = parent;       
                                        found = true;
                                 }
                                 f2 = current;
                           }
                           parent = current;
                           current = current->right;     
                    } /* End of if condition pre->right == NULL */
             } /* End of if condition current->left == NULL*/
       } /* End of while */

       if(f1 && f2)
             swap(f1->val, f2->val);
}
```

参考：  
[http://fisherlei.blogspot.jp/2012/12/leetcode-recover-binary-search-tree.html](http://fisherlei.blogspot.jp/2012/12/leetcode-recover-binary-search-tree.html)
[http://blog.csdn.net/worldwindjp/article/details/21694179](http://blog.csdn.net/worldwindjp/article/details/21694179)
[http://blog.163.com/ya_mzy/blog/static/19959325520137153340725/](http://blog.163.com/ya_mzy/blog/static/19959325520137153340725/)
[http://www.2cto.com/kf/201310/251524.html](http://www.2cto.com/kf/201310/251524.html)