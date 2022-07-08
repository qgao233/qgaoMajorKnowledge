# 多叉树的两种遍历区别

```
//正确打印所有节点的进入和离开信息
void traverse(TreeNode root) {  
    if (root == null) return;  
    System.out.println("enter: " + root.val);  
    for (TreeNode child : root.children) {  
        traverse(child);  
    }  
    System.out.println("leave: " + root.val);  
}  
//少打印整棵树根节点的进入和离开信息  
void traverse(TreeNode root) {  
    if (root == null) return;  
    for (TreeNode child : root.children) {  
        System.out.println("enter: " + child.val);  
        traverse(child);  
        System.out.println("leave: " + child.val);  
    }  
}  
```

前者会正确打印所有节点的进入和离开信息，而后者唯独会少打印整棵树根节点的进入和离开信息。

为什么回溯算法框架会用后者？因为回溯算法关注的不是节点，而是树枝，不信你看 回溯算法核心套路 里面的图，它可以忽略根节点。
