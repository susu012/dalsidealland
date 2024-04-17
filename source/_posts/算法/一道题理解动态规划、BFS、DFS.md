---
title: 一道题理解动态规划、BFS、DFS
date: 2023-04-06 20:19:44
tags: 
categories:
  - - 算法
auto_excerpt: "true"
---
## **题目**

这里有一道能同时被动归、BFS、DFS解决的问题：

> 给定一个m*n的网格和一个机器人的初始位置（0,0），机器人每次只能向下或者向右移动一步，问有多少种不同的路径可以到达网格右下角？

---

## **动态规划**

自然语言过程：定义二维表，初始化，两个for，return

口诀：表，初，for，return【表初 for return】

```java
public int uniquePaths(int m, int n) {
    //定义状态数组dp，dp[i][j]表示从(0,0)到(i,j)的不同路径数
    int[][] dp = new int[m][n];
  
    //初始化第一行和第一列，因为只能向右或向下走，所以这些位置的路径数都是1
    for(int i = 0; i < m; i++){
        dp[i][0] = 1;
    }
    for(int j = 0; j < n; j++){
        dp[0][j] = 1;
    }
  
    //动态规划求解
    for(int i = 1; i < m; i++){
        for(int j = 1; j < n; j++){
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
    }
  
    //返回最终结果，即从(0,0)到(m-1,n-1)的路径数
    return dp[m-1][n-1];
}
```

---

## **BFS**

BFS是用队列加塞的方式顺带记录遍历探索的分支

自然语言过程：创建队列，添加起点，初始化计数器，非队空时循环，弹一个我就末尾计数，或者探索这个的周围并加塞

口诀：声明队列，起点加塞，计数器，非空循环，弹一个计数或者探索周围

```java
import java.util.LinkedList;
import java.util.Queue;

public class BFS {
    // 定义一个方法，用来计算机器人在网格中从起点走到终点的路径数量
    public int uniquePaths(int m, int n) {
        // 创建一个队列，用来存储位置信息
        Queue<int[]> queue = new LinkedList<>();
        // 将起点(0,0)添加到队列中
        queue.offer(new int[]{0, 0});
        // 定义一个变量，用来计数路径的数量
        int count = 0;
        // 当队列不为空时循环，即还有位置未被搜索
        while (!queue.isEmpty()) {
            // 取出队列中的一个位置信息
            int[] cur = queue.poll();
            // 如果该位置是终点，则将路径计数器加1
            if (cur[0] == m - 1 && cur[1] == n - 1) {
                count++;
            } else {
                // 否则，将当前位置向下或向右移动，并将可能到达的位置添加到队列中
                if (cur[0] + 1 < m) {
                    queue.offer(new int[]{cur[0] + 1, cur[1]});
                }
                if (cur[1] + 1 < n) {
                    queue.offer(new int[]{cur[0], cur[1] + 1});
                }
            }
        }
        // 返回计数器中存储的路径数量
        return count;
    }

    // 主方法，用于测试uniquePaths方法
    public static void main(String[] args) {
        // 创建一个BFS对象
        BFS solution = new BFS();
        // 输出机器人在7 x 9网格上移动时到达右下角的不同路径数量
        System.out.println(solution.uniquePaths(7,9));
    }
}
```

---

## **DFS**

DFS用递归的方式，在栈中记录下探索的分支

自然语言过程：dfs函数，形参输入起点，如果到终点就计数且return，如果往下或者往右不越界就dfs下一个或者右一个

定义dfs函数：以形参做起点，如果终点计数加return，不越界就dfs下一个位置

```java
public class DFS{
    // 定义网格的行数和列数
    private int m, n;
    // 定义计数器，用来记录不同路径的数量
    private int count = 0;

    public int uniquePaths(int m, int n) {
        // 初始化网格的行数和列数
        this.m = m;
        this.n = n;
        // 从起点（0,0）开始搜索
        dfs(0, 0);
        // 返回最终结果
        return count;
    }

    private void dfs(int x, int y) {
        // 如果到达终点，计数器加1
        if (x == m - 1 && y == n - 1) {
            count++;
            return;
        }

        // 向右移动一步，如果没有越界，继续搜索
        if (x + 1 < m) {
            dfs(x + 1, y);
        }

        // 向下移动一步，如果没有越界，继续搜索
        if (y + 1 < n) {
            dfs(x, y + 1);
        }
    }

    public static void main(String[] args) {
        DFS solution = new DFS();
        System.out.println(solution.uniquePaths(4,11));
    }
}
```

BFS是弹出一个然后探索这个的周围并加塞，DFS是达不到就递归
