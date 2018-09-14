> 桶排序（Bucket sort）或所谓的箱排序，是一个排序算法。
>
> 假设有一组长度为N的待排关键字序列K\[1....n\]。首先将这个序列划分成M个的子区间\(桶\) 。然后基于某种映射函数 ，将待排序列的关键字k映射到第i个桶中\(即桶数组B的下标 i\) ，那么该关键字k就作为B\[i\]中的元素。接着对每个桶B\[i\]中的所有元素进行比较排序\(可以使用快排\)。然后依次枚举输出B\[0\]....B\[M\]中的全部内容即是一个有序序列。

桶排序的步骤：

1. 设置一个定量的数组当作空桶子。

2. 寻访序列，并且把项目一个一个放到对应的桶子去。

3. 对每个不是空的桶子进行排序。

4. 从不是空的桶子里把项目再放回原来的序列中。

**性能**  
数据结构 数组　 最差时间复杂度 　 O\(n2\)平均时间复杂度 　O\(n+k\) 最差空间复杂度　O\(n\*k\)

平均情况下桶排序以线性时间运行，桶排序是稳定的，排序非常快,但是同时也非常耗空间,基本上是最耗空间的一种排序算法。

对N个关键字进行桶排序的时间复杂度分为两个部分：

1. 循环计算每个关键字的桶映射函数，这个时间复杂度是O\(N\)。

2. 利用先进的比较排序算法对每个桶内的所有数据进行排序，其时间复杂度为 ∑ O\(Ni\*logNi\) 。其中Ni 为第i个桶的数据量。

很显然，第②部分是桶排序性能好坏的决定因素。尽量减少桶内数据的数量是提高效率的唯一办法\(因为基于比较排序的最好平均时间复杂度只能达到O\(N\*logN\)了\)。因此，我们需要尽量做到下面两点：

> 1. 映射函数f\(k\)能够将N个数据平均的分配到M个桶中，这样每个桶就有\[N/M\]个数据量。
>
> 2. 尽量的增大桶的数量。极限情况下每个桶只能得到一个数据，这样就完全避开了桶内数据的“比较”排序操作。 当然，做到这一点很不容易，数据量巨大的情况下，f\(k\)函数会使得桶集合的数量巨大，空间浪费严重。这就是一个时间代价和空间代价的权衡问题了。

**Java实现**

```java
public class BucketSort  {
    /** 
     * 对arr进行桶排序，排序结果仍放在arr中 
     */  
    public static void bucketSort(double arr[]){  
  //-------------------------------------------------分桶-----------------------------------------------      
        int n = arr.length;  
        //存放桶的链表
        ArrayList bucketList[] = new ArrayList [n];  
        //每个桶是一个list，存放此桶的元素   
        for(int i =0;i<n;i++){  
            //下取等
            int temp = (int) Math.floor(n*arr[i]);  
            //若不存在该桶，就新建一个桶并加入到桶链表中
            if(null==bucketList[temp])  
                bucketList[temp] = new ArrayList();  
            //把当前元素加入到对应桶中
            bucketList[temp].add(arr[i]);            
        }  
//-------------------------------------------------桶内排序-----------------------------------------------    
        //对每个桶中的数进行插入排序   
        for(int i = 0;i<n;i++){  
            if(null!=bucketList[i])  
                insert(bucketList[i]);  
        }  
//-------------------------------------------------合并桶内数据-----------------------------------------------    
        //把各个桶的排序结果合并   
        int count = 0; 
        for(int i = 0;i<n;i++){  
            if(null!=bucketList[i]){  
                Iterator iter = bucketList[i].iterator();  
                while(iter.hasNext()){  
                    Double d = (Double)iter.next();  
                    arr[count] = d;  
                    count++;  
                }  
            }  
        }  
    }  
    
    /** 
     * 用插入排序对每个桶进行排序 
     * 从小到大排序
     */  
    public static void insert(ArrayList list){  
        
        if(list.size()>1){  
            for(int i =1;i<list.size();i++){  
                if((Double)list.get(i)<(Double)list.get(i-1)){  
                    double temp = (Double) list.get(i);  
                    int j = i-1;  
                    for(;j>=0&&((Double)list.get(j)>(Double)list.get(j+1));j--)  
                        list.set(j+1, list.get(j));  //后移
                    list.set(j+1, temp);  
                }  
            }  
        } 
    }
}
测试代码：

public static void main(String[] args) {
        double arr [] ={0.21,0.23,0.76,0.12,0.89};
        BucketSort.bucketSort(arr);
        for(double a:arr){
            System.out.println(a);
        }
}
```



