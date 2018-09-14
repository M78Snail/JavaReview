> 堆排序\(Heapsort\)是指利用堆这种数据结构所设计的一种排序算法，它是选择排序的一种。可以利用数组的特点快速定位指定索引的元素。堆分为大根堆和小根堆，是完全二叉树。大根堆的要求是每个节点的值都不大于其父节点的值。
>
> 由于堆中每次都只能删除第0个数据，通过　取出第０个数据再执行堆的删除操作、重建堆（实际的操作是将最后一个数据的值赋给根结点，然后再从根结点开始进行一次从上向下的调整。），然后再取，如此重复实现排序。

**堆的操作：**

| ![](/assets/import6.1.png) |
| :---: |


在堆的数据结构中，堆中的最大值总是位于根节点。堆中定义以下几种操作：

* 最大堆调整（Max\_Heapify）：将堆的末端子节点作调整，使得子节点永远小于父节点

* 创建最大堆（Build\_Max\_Heap）：将堆所有数据重新排序

* 堆排序（HeapSort）：移除位在第一个数据的根节点，并做最大堆调整的递归运算

**堆的存储：**

| ![](/assets/import6.2.png) |
| :---: |


通常堆是通过一维数组来实现的。在数组起始位置为0的情形中：

* 父节点i的左子节点在位置\(2\*i+1\);

* 父节点i的右子节点在位置\(2\*i+2\);

* 子节点i的父节点在位置floor\(\(i-1\)/2\);

**Java代码实现**

```java
public class HeapSort {
    private static int[] sort = new int[]{1,0,10,20,3,5,6,4,9,8,12,17,34,11};
    public static void main(String[] args) {
        buildMaxHeapify(sort);
        heapSort(sort);
        print(sort);
    }

    private static void buildMaxHeapify(int[] data){
        //没有子节点的才需要创建最大堆，从最后一个的父节点开始
        int startIndex = getParentIndex(data.length - 1);
        //从尾端开始创建最大堆，每次都是正确的堆
        for (int i = startIndex; i >= 0; i--) {
            maxHeapify(data, data.length, i);
        }
    }

    /**
     * 创建最大堆
     * @param data
     * @param heapSize需要创建最大堆的大小，一般在sort的时候用到，因为最多值放在末尾，末尾就不再归入最大堆了
     * @param index当前需要创建最大堆的位置
     */
    private static void maxHeapify(int[] data, int heapSize, int index){
        // 当前点与左右子节点比较
        int left = getChildLeftIndex(index);
        int right = getChildRightIndex(index);

        int largest = index;
        if (left < heapSize && data[index] < data[left]) {
            largest = left;
        }
        if (right < heapSize && data[largest] < data[right]) {
            largest = right;
        }
        //得到最大值后可能需要交换，如果交换了，其子节点可能就不是最大堆了，需要重新调整
        if (largest != index) {
            int temp = data[index];
            data[index] = data[largest];
            data[largest] = temp;
            maxHeapify(data, heapSize, largest);
        }
    }

    /**
     * 排序，最大值放在末尾，data虽然是最大堆，在排序后就成了递增的
     * @param data
     */
    private static void heapSort(int[] data) {
        //末尾与头交换，交换后调整最大堆
        for (int i = data.length - 1; i > 0; i--) {
            int temp = data[0];
            data[0] = data[i];
            data[i] = temp;
            maxHeapify(data, i, 0);
        }
    }

    /**
     * 父节点位置
     * @param current
     * @return
     */
    private static int getParentIndex(int current){
        return (current - 1) >> 1;
    }

    /**
     * 左子节点position注意括号，加法优先级更高
     * @param current
     * @return
     */
    private static int getChildLeftIndex(int current){
        return (current << 1) + 1;
    }

    /**
     * 右子节点position
     * @param current
     * @return
     */
    private static int getChildRightIndex(int current){
        return (current << 1) + 2;
    }

    private static void print(int[] data){
        for (int i = 0; i < data.length; i++) {
            System.out.print(data[i] + " |");
        }
    }
}
```



