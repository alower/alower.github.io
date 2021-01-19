---
title: 1584.连接所有点的最小费用---Kruskal并查集
date: 2021-01-19 19:49:43
tags:
---

### 题目

给你一个points 数组，表示 2D 平面上的一些点，其中 points[i] = [xi, yi] 。

连接点 [xi, yi] 和点 [xj, yj] 的费用为它们之间的 曼哈顿距离 ：|xi - xj| + |yi - yj| ，其中 |val| 表示 val 的绝对值。

请你返回将所有点连接的最小总费用。只有任意两点之间 有且仅有 一条简单路径时，才认为所有点都已连接。

![](http://myblogoss.aimezhao.online/20210119200443.png)

实例1
```
输入：points = [[0,0],[2,2],[3,10],[5,2],[7,0]]
输出：20
```
解释：

![](http://myblogoss.aimezhao.online/20210119200501.png)

### 题解

1. 所有点连接的最小费用，对于每一条边，假设曼哈顿距离为其边权，那么只需要找到能够连接所有点的最小边权的边。所以，我们需要对边权进行排序（从小到大）
2. 拿到已经排序好的边权时，判断每条边的两个顶点是否属于一个连通图
   1. 若属于一个连通图，直接跳过`(之前两点已经加入过该连通图，表示有边权比其小的连接方式)`
   2. 若不属于一个连通图，则合并这两个连通分量
   3. 结束条件：直到一个连通分量包含所有的点

```java
class Solution {
    public int minCostConnectPoints(int[][] points) {
        int len = points.length;
        PriorityQueue<int[]> priorityQueue = new PriorityQueue<>(new Comparator<int[]>() {
            @Override
             public int compare(int[] o1, int[] o2) {
                 return o1[2] - o2[2];
             }
        });
        for(int i = 0; i < len; i++) {
            for(int j = i + 1; j < len; j++) {
                int dis = Math.abs(points[j][1]-points[i][1]) + Math.abs(points[j][0]-points[i][0]);
                priorityQueue.offer(new int[]{i, j, dis});
            }
        }
        // ---------此时已经拿到排序过的边权和其对应的两个点-------
        UnionFind myUnionFind = new UnionFind(len);
        // 记录边的个数
        int count = 0;
        int res = 0;
        while(!priorityQueue.isEmpty() && cout < len - 1) {
            int[] cur = priorityQueue.poll();
            int start = cur[0];
            int end = cur[1];
            int dis = cur[2];
            if(myUnionFind(start) != myUnionFind(end)) {
                // 合并两个连通分量
                myUnionFind.union(start, end);
                count++;
                res += dis;
            }
        }
        return res;

    }

    class UnionFind {
        int[] par;

        public UnionFind(int n) {
            par = new int[n];
            for(int i = 0; i < n; i++) {
                par[i] = i;
            }
        }

        public union(int a, int b) {
            int rootA = find(a);
            int rootB = find(b);
            if(rootA != rootB) {
                if(par[rootA] < par[rootB]) {
                    par[rootB] = rootA;
                } else {
                    if(par[rootA] == par[rootB]) {
                        par[rootB]--;
                    }
                    par[rootA] = rootB;
                }
            }
        }

        public int find(int index) {
            if(par[index] != index) {
                return find(par[index]);
            }
            return par[index];
        }
    }
}
```
