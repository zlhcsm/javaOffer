求树高度我们可以采用`深度遍历二叉树`和`层次遍历二叉树`
### 题目描述
>输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。
### 递归版深度遍历
```java
class Solution{
    public int TreeDepth(TreeNode root){
        // 递归到null，返回0即可
        if (root == null)
            return 0;
        int left = TreeDepth(root.left);
        int right = TreeDepth(root.right);
        // 这一层最大值就是左右子树最大值 + 1
        int ans = Math.max(left, right) + 1;
   
        return ans;
    }
}
```
### 非递归层次遍历
`思路说明：`常规版的非递归遍历二叉树，我们很了解，那么这里不一样的地方，就是我们无法判断何时到了
下一层！解决方案是加一个标志位，然后不断更改每一层中的值
```java
import java.util.*;
class Solution{
    public int TreeDepth(TreeNode root){
        if (root == null)
            return 0;
        LinkedList<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        // count是现在有几个节点，nextCount是下一层有几个节点，当他们相等，则depth+1
        int count = 0, nextCount = 1,depth = 0;
        while (!queue.isEmpty()){
            TreeNode node = queue.pop();
            count++;
            if (node.left != null)
                queue.add(node.left);
            if(node.right != null)
                queue.add(node.right);
            if (count == nextCount){
                nextCount = queue.size();
                count = 0;
                depth++;
            }
        }
        return depth;
    }
}
```
