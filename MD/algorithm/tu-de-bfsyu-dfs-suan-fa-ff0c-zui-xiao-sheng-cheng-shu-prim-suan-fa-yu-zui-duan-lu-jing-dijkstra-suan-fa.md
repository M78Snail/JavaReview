图G是由顶点的有穷集合，以及顶点之间的关系组成，顶点的集合记为V，顶点之间的关系构成边的集合E，G=\(V,E\).

　　如果给图的每条边规定一个方向，那么得到的图称为有向图，其边也称为有向边。在有向图中，与一个节点相关联的边有出边和入边之分，而与一个有向边关联的两个点也有始点和终点之分。相反，边没有方向的图称为无向图。  
**图的遍历 **

> **DFS（Depth First Search）深度优先搜索**

为每个顶点设立一个“访问标志”。首先将图中每个顶点的访问标志设为 FALSE, 之后搜索图中每个顶点，如果未被访问，则以该顶点为起始点，进行遍历。若当前访问的顶点的邻接顶点有未被访问的，则任选一个访问之。反之，退回到最近访问过的顶点；直到与起始顶点相通的全部顶点都访问完毕；

> 遍历图的过程实质上是对每个顶点查找其邻接点的过程，所耗费的时间取决于所采用的存储结构。 对图中的每个顶点至多调用1次DFS算法，因为一旦某个顶点已访问过，则不再从它出发进行搜索。

> 邻接链表表示：查找每个顶点的邻接点所需时间为O\(e\)，e为边\(弧\)数，算法时间复杂度为O\(n+e\)

> 数组表示：查找每个顶点的邻接点所需时间为O\(n2\)，n为顶点数，算法时间复杂度为O\(n2\)

---

> **BFS（Breadth First Search）广度优先遍历**

从图的某一结点出发，首先依次访问该结点的所有邻接顶点 Vi1, Vi2, …, Vin 再按这些顶点被访问的先后次序依次访问与它们相邻接的所有未被访问的顶点，重复此过程，直至所有顶点均被访问为止。

**遍历代码（邻接矩阵）**

```java
// 邻接矩阵存储图
public class Graph {
    // 顶点数
    private int number = 9;
    // 记录顶点是否被访问
    private boolean[] flag;
    // 顶点
    private String[] vertexs = { "A", "B", "C", "D", "E", "F", "G", "H", "I" };
    // 边
    private int[][] edges = { 
            { 0, 1, 0, 0, 0, 1, 1, 0, 0 }, 
            { 1, 0, 1, 0, 0, 0, 1, 0, 1 }, 
            { 0, 1, 0, 1, 0, 0, 0, 0, 1 },
            { 0, 0, 1, 0, 1, 0, 1, 1, 1 },
            { 0, 0, 0, 1, 0, 1, 0, 1, 0 }, 
            { 1, 0, 0, 0, 1, 0, 1, 0, 0 },
            { 0, 1, 0, 1, 0, 1, 0, 1, 0 },
            { 0, 0, 0, 1, 1, 0, 1, 0, 0 }, 
            { 0, 1, 1, 1, 0, 0, 0, 0, 0 } 
            };

    // 图的深度遍历操作(递归)
    void DFSTraverse() {
        flag = new boolean[number];
        for (int i = 0; i < number; i++) {
            if (flag[i] == false) {// 当前顶点没有被访问
                DFS(i);
            }
        }
    }

    // 图的深度优先递归算法
    void DFS(int i) {
        flag[i] = true;// 第i个顶点被访问
        System.out.print(vertexs[i] + " ");
        for (int j = 0; j < number; j++) {
            if (flag[j] == false && edges[i][j] == 1) {
                DFS(j);
            }
        }
    }

    // 图的广度遍历操作
    void BFSTraverse() {
        flag = new boolean[number];
        Queue<Integer> queue = new LinkedList<Integer>();
        for (int i = 0; i < number; i++) {
            if (flag[i] == false) {
                flag[i] = true;
                System.out.print(vertexs[i] + " ");
                queue.add(i);
                while (!queue.isEmpty()) {
                    int j = queue.poll();
                    for (int k = 0; k < number; k++) {
                        if (edges[j][k] == 1 && flag[k] == false) {
                            flag[k] = true;
                            System.out.print(vertexs[k] + " ");
                            queue.add(k);
                        }
                    }
                }
            }
        }
    }

    public static void main(String[] args) {
        Graph graph = new Graph();
        System.out.println("-----------DFS-----------------");
        graph.DFSTraverse();
        System.out.println();
        System.out.println("-----------BFS-----------------");
        graph.BFSTraverse();
    }
}
```

运行结果：

```
------------DFS------------
ABCDEFGHI
------------BFS------------
ABFGCIEDH
```



