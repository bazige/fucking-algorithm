# DFS与BFS

## 1. DFS

深度优先通过**栈处理**。我们所熟悉的 DFS（深度优先搜索）问题通常是在树或者图结构上进行的。而我们今天要讨论的 DFS 问题，是在一种「网格」结构中进行的——也就是岛屿问题。

网格结构要比二叉树结构稍微复杂一些，它其实是一种简化版的**图**结构。二叉树的 DFS 有两个要素：「**访问相邻结点**」和「**判断 base case**」。

二叉树的相邻结点非常简单，只有左子结点和右子结点两个。二叉树本身就是一个递归定义的结构：一棵二叉树，它的左子树和右子树也是一棵二叉树。那么我们的 DFS 遍历只需要递归调用左子树和右子树即可。而网格问题是从上、下、左、右四个方向拓展开的，可以称为四叉树问题。有时也要考虑斜对角，也称为八叉树问题。

![网格结构中四个相邻的格子](https://pic.leetcode-cn.com/63f5803e9452ccecf92fa64f54c887ed0e4e4c3434b9fb246bf2b410e4424555.jpg)

一般来说，二叉树遍历的 base case 是 root == null。这样一个条件判断其实有两个含义：一方面，这表示 root 指向的子树为空，不需要再往下遍历了。另一方面，在 root == null 的时候及时返回，可以让后面的 root.left 和 root.right 操作不会出现空指针异常。网格 DFS 中的 base case 是什么？从二叉树的 base case 对应过来，应该是网格中不需要继续遍历、grid[r][c] 会出现数组下标越界异常的格子，也就是那些超出网格范围的格子。

![网格 DFS 的 base case](https://pic.leetcode-cn.com/5a91ec351bcbe8e631e7e3e44e062794d6e53af95f6a5c778de369365b9d994e.jpg)

网格 DFS 遍历的框架代码：

如何避免这样的重复遍历呢？答案是标记已经遍历过的格子。以岛屿问题为例，我们需要在所有值为 1 的陆地格子上做 DFS 遍历。每走过一个陆地格子，就把格子的值改为 2，这样当我们遇到 2 的时候，就知道这是遍历过的格子了。也就是说，每个格子可能取三个值：

0 —— 海洋格子
1 —— 陆地格子（未遍历过）
2 —— 陆地格子（已遍历过）

```java
void dfs(int[][] grid, int r, int c) {
    // 判断 base case
    if (!inArea(grid, r, c)) {
        return;
    }
    // 如果这个格子不是岛屿，直接返回
    if (grid[r][c] != 1) {
        return;
    }
    grid[r][c] = 2; // 将格子标记为「已遍历过」
    
    // 访问上、下、左、右四个相邻结点
    dfs(grid, r - 1, c);
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}

// 判断坐标 (r, c) 是否在网格中
boolean inArea(int[][] grid, int r, int c) {
    return 0 <= r && r < grid.length 
        	&& 0 <= c && c < grid[0].length;
}

```



## 2.BFS

广度优先通过**队列处理**（`from collecions import deque`），基本流程是：

```
1. 将一层记录在队列中，并记录队列数组长度。
2. 开始循环处理队列数组中节点：
		3. 将数组首位弹出 
		4. 首位节点操作
		5. 将首位的左右节点追在队列数组后
按照记录的数组长度 将上层的结点全部弹出后 此时数组只剩下下一行结点了 此时就完成了一层的遍历
```

```python
from collections import deque
class Solution:
    def connect(self, root: 'Node') -> 'Node':
      	#特殊情况处理
				if root is None:
            return root
        stack = deque([root])
        #循环处理队列中一层所有节点进行处理
        while stack:
            #你的操作也可以防在此处
            #------
            lens = len(stack)
            #循环处理队列中所有节点
            for i in range(lens):
                #弹出首个节点
                node = stack.popleft()
                #你也可以在这进行相关操作
                if i < lens - 1:
                    node.next = stack[0]
                    
								#首位节点的左右节点放在队列中
                if node.left:
                    stack.append(node.left)
                if node.right:
                    stack.append(node.right)
        return root
```



# 3.回溯算法

**解决一个回溯问题，实际上就是一个决策树的遍历过程**。你只需要思考 3 个问题：

1、路径：也就是已经做出的选择。

2、选择列表：也就是你当前可以做的选择。

3、结束条件：也就是到达决策树底层，无法再做选择的条件。

如果你不理解这三个词语的解释，没关系，我们后面会用「全排列」和「N 皇后问题」这两个经典的回溯算法问题来帮你理解这些词语是什么意思，现在你先留着印象。

回溯算法的框架：

```python
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return

    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

**其核心就是 for 循环里面的递归，在递归调用之前「做选择」，在递归调用之后「撤销选择」**，特别简单。

**写 `backtrack` 函数时，需要维护走过的「路径」和当前可以做的「选择列表」，当触发「结束条件」时，将「路径」记入结果集**。另外需要注意的是回溯问题无重叠的子问题。