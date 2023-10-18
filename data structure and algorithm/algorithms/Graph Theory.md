[toc]





&emsp;
&emsp;
# 1. 

# 2. 图的遍历
## 2.0 图解DFS和BFS
<div align="center"> <img src="./pic/dfs_bfs.jpeg"> </div>

## 2.1 深度优先(Depth First Search, DFS)

## 2.2 广度优先(Breadth First Search, BFS)
BFS 可以看成是层序遍历。从某个结点出发，BFS 首先遍历到距离为 1 的结点，然后是距离为 2、3、4…… 的结点。因此，BFS 可以用来求最短路径问题。BFS 先搜索到的结点，一定是距离最近的结点。
### BFS适合的场景
最短路径

## 2.3 实例讲解DFS和BFS：
### (1) 岛屿数量
题目：[200. 岛屿数量](https://leetcode.cn/problems/number-of-islands/?envType=study-plan-v2&envId=top-100-liked)
解法：
```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        def dfs(r, c):
            # 判断边界
            if  r < 0 or r >= len(grid) or c < 0 or c >= len(grid[0]):
                return
            # 改为0也可以，但是这样就不能区分陆地和水了
            if grid[r][c] == "1": 
                grid[r][c] = "2" 
            elif grid[r][c] == "0" or grid[r][c] == "2":
                return
            # 深度优先遍历(r, c)上下左右四个节点
            dfs(r-1, c), dfs(r+1, c), dfs(r, c-1), dfs(r, c+1)

        cnt = 0
        for r in range(len(grid)):
            for c in range(len(grid[0])):
                print(r, c)
                if grid[r][c] == "1":
                    cnt += 1
                    dfs(r, c)                    
        return cnt
```
### (2) 腐烂的橘子
题目：[994. 腐烂的橘子](https://leetcode.cn/problems/rotting-oranges/?envType=study-plan-v2&envId=top-100-liked)
解法：
使用BFS
**为何使用BFS？**
> &emsp;&emsp; BFS 可以看成是层序遍历。从某个结点出发，BFS 首先遍历到距离为 1 的结点，然后是距离为 2、3、4…… 的结点。因此，BFS 可以用来求最短路径问题。BFS 先搜索到的结点，一定是距离最近的结点。
> &emsp;&emsp; 再看看这道题的题目要求：返回直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。翻译一下，实际上就是求腐烂橘子到所有新鲜橘子的最短路径。那么这道题使用 BFS，应该是毫无疑问的了。
> 
```python

```





