从单一顶点开始，普里姆算法按照以下步骤逐步扩大树中所含顶点的数目，直到遍及连通图的所有顶点。

1. 输入：一个加权连通图，其中顶点集合为V，边集合为E；

2. 初始化：Vnew = {x}，其中x为集合V中的任一节点（起始点），Enew = {}；

3. 重复下列操作，直到Vnew = V：

   3.1 在集合E中选取权值最小的边（u, v），其中u为集合Vnew中的元素，而v则是V中没有加入Vnew的顶点（如果存在有多条满足前述条件即具有相同权值的边，则可任意选取其中之一）

   3.2 将v加入集合Vnew中，将（u, v）加入集合Enew中；

4. 输出：使用集合Vnew和Enew来描述所得到的最小生成树。

> 此为原始的加权连通图。每条边一侧的数字代表其权值。

| ![](/assets/import6.15.1.png) |
| :---: |


> 顶点D被任意选为起始点。顶点A、B、E和F通过单条边与D相连。A是距离D最近的顶点，因此将A及对应边AD以高亮表示。

| ![](/assets/import6.15.2.png) |
| :---: |


> 下一个顶点为距离D或A最近的顶点。B距D为9，距A为7，E为15，F为6。因此，F距D或A最近，因此将顶点F与相应边DF以高亮表示。

| ![](/assets/import6.15.3.png) |
| :---: |


> 算法继续重复上面的步骤。距离A为7的顶点B被高亮表示。

| ![](/assets/import6.15.4.png) |
| :---: |


> 在当前情况下，可以在C、E与G间进行选择。C距B为8，E距B为7，G距F为11。E最近，因此将顶点E与相应边BE高亮表示。

| ![](/assets/import6.15.5.png) |
| :---: |


> 这里，可供选择的顶点只有C和G。C距E为5，G距E为9，故选取C，并与边EC一同高亮表示。

| ![](/assets/import6.15.6.png) |
| :---: |


> 顶点G是唯一剩下的顶点，它距F为11，距E为9，E最近，故高亮表示G及相应边EG。

| ![](/assets/import6.15.7.png) |
| :---: |


> 现在，所有顶点均已被选取，图中绿色部分即为连通图的最小生成树。在此例中，最小生成树的权值之和为39。

| ![](/assets/import6.15.8.png) |
| :---: |


**Java实现**

```java
public class Prim {
    //结点集
    public static List<Vertex> vertexList = new ArrayList<Vertex>();
    //边集
    public static List<Edge> EdgeList = new ArrayList<Edge>();
    //已经访问过的结点集
    public static List<Vertex> containVertexList = new ArrayList<Vertex>();
    
    public static void main(String[] args) {
        primTree();
    }
    public static void primTree(){
        //初始化图
        buildGraph();
        //起始点
        Vertex start = vertexList.get(0);
        containVertexList.add(start);
        for(int n=0;n<vertexList.size()-1;n++){
            //临时节点a
            Vertex temp = new Vertex(start.key);
            Edge tempedge = new Edge(start,start,1000);
            
            for(Vertex v : containVertexList){
                for(Edge e : EdgeList){
                    //找出相邻最小边
                    if(e.start==v && !containVertex(e.end)){
                        if(e.Len<tempedge.Len){
                            temp = e.end;
                            tempedge = e;
                        }
                    }
                }
            }
            //把该点加入
            containVertexList.add(temp);
        }
        
        //打印输出
        Iterator it = containVertexList.iterator();
        while(it.hasNext()){
            Vertex v =(Vertex) it.next();
            System.out.println(v.key);
        }
    }
    
    public static void buildGraph() {
        Vertex v1 = new Vertex("a");
        Prim.vertexList.add(v1);
        Vertex v2 = new Vertex("b");
        Prim.vertexList.add(v2);
        Vertex v3 = new Vertex("c");
        Prim.vertexList.add(v3);
        Vertex v4 = new Vertex("d");
        Prim.vertexList.add(v4);
        Vertex v5 = new Vertex("e");
        Prim.vertexList.add(v5);
        addEdge(v1, v2, 6);
        addEdge(v1, v3, 7);
        addEdge(v2, v3, 8);
        addEdge(v2, v5, 4);
        addEdge(v2, v4, 5);
        addEdge(v3, v4, 3);
        addEdge(v3, v5, 9);
        addEdge(v5, v4, 7);
        addEdge(v5, v1, 2);
        addEdge(v4, v2, 2);
    }
    public static void addEdge(Vertex a, Vertex b, int w) {
        Edge e = new Edge(a, b, w);
        Prim.EdgeList.add(e);
    }
    public static boolean containVertex(Vertex vte){
        for(Vertex v : containVertexList){
            if(v.key.equals(vte.key))
                return true;
        }
        return false;
    }
}
class Vertex {
    String key;
    Vertex(String key){
        this.key = key;
    }
}
class Edge{
    Vertex start;
    Vertex end;
    int Len;
    Edge(Vertex start,Vertex end,int key){
        this.start = start;
        this.end  = end;
        this.Len = key;
        
    }
}
```



