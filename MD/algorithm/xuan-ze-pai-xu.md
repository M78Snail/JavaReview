> 常用的选择排序方法有简单选择排序和堆排序，这里只说简单选择排序，堆排序后面再说。

**简单选择排序**

> 设所排序序列的记录个数为n，i　取　1,2,…,n-1　。 　　从所有n-i+1个记录（Ri,Ri+1,…,Rn）中找出排序码最小（或最大）的记录，与第i个记录交换。执行n-1趟 后就完成了记录序列的排序。

以排序数组｛3，2，1，4，6，5｝为例

| ![](/assets/import5.2.png) | ![](/assets/import5.2.1.png) |
| :---: | :---: |


---

**简单选择排序性能**

在简单选择排序过程中，所需移动记录的次数比较少。最好情况下，即待排序记录初始状态就已经是正序排列了，则不需要移动记录。最坏情况下，即待排序记录初始状态是按第一条记录最大，之后的记录从小到大顺序排列，则需要移动记录的次数最多为3（n-1）。

简单选择排序过程中需要进行的比较次数与初始状态下待排序的记录序列的排列情况无关。 　　当i=1时，需进行n-1次比较；当i=2时，需进行n-2次比较；依次类推，共需要进行的比较次数是\(n-1\)+\(n-2\)+…+2+1=n\(n-1\)/2，即进行比较操作的时间复杂度为O\( n2\)，进行移动操作的时间复杂度为O\(n\)。　

简单选择排序是不稳定排序。

  
**简单选择排序Java实现**

```java
public static void main(String[] args) {
        int[] number = {3,1,2,8,4,5,24,12};
        SimpleSort(number);
        for(int i = 0; i < number.length; i++) {
            System.out.print(number[i] + " ");
            }
    }
public static void SimpleSort(int[] arr) {
            int length=arr.length;
            int temp;
            for(int i=0;i<length-1;i++){
                int min=i;
                for(int j=i+1;j<length;j++){ //寻找最小的数
                    if(arr[j]<arr[min]){
                        min =j;
                    }
                }
                if(min!=i){
                     temp = arr[min];
                     arr[min]=arr[i];
                     arr[i]=temp;
                }
            }
}   
```





