# 图的遍历

显然，对于这里「图」的遍历，我们应该把visited的操作放到 for 循环外面，否则会漏掉起始点的遍历。

当然，当有向图含有环的时候才需要visited数组辅助，如果不含环，连visited数组都省了，基本就是多叉树的遍历。

```
Graph graph;  
boolean[] visited;  
  
/* 图遍历框架 */  
void traverse(Graph graph, int s) {  
    if (visited[s]) return;  
    // 经过节点 s  
    visited[s] = true;  
    for (TreeNode neighbor : graph.neighbors(s))  
        traverse(neighbor);  
    // 离开节点 s  
    visited[s] = false;     
}  
```