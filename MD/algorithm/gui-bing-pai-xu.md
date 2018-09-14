> 归并排序，是创建在归并操作上的一种有效的排序算法该算法是采用分治法（Divide and Conquer）的一个非常典型的应用，且各层分治递归可以同时进行。
>
> 即先使每个子序列有序，再将两个已经排序的序列合并成一个序列的操作。若将两个有序表合并成一个有序表，称为二路归并。

---

> 设有数列{6，202，100，301，38，8，1} 初始状态：6,202,100,301,38,8,1
>
> 第一次归并后：{6,202},{100,301},{8,38},{1}，比较次数：3；
>
> 第二次归并后：{6,100,202,301}，{1,8,38}，比较次数：4；
>
> 第三次归并后：{1,6,8,38,100,202,301},比较次数：4；
>
> 总的比较次数为：3+4+4=11,； 逆序数为14；

**迭代实现的实现原理**

1. 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列

2. 设定两个指针，最初位置分别为两个已经排序序列的起始位置

3. 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置

4. 重复步骤③直到某一指针到达序列尾

5. 将另一序列剩下的所有元素直接复制到合并序列尾

**Java代码**

```java
public static void main(String[] args) {
        int [] arr ={6,5,3,1,8,7,2,4};
        merge_sort(arr);
        for(int i : arr){
            System.out.println(i);
        }
}
public static void merge_sort(int[] arr) {
        int len = arr.length;
      //用于合并的临时数组
        int[] result = new int[len];
        int block, start;

      //两两合并后块大小变大两倍 (注意最后一次block等于len)
        for(block = 1; block <=len ; block *= 2) {
            //把整个数组分成很多个块，每次合并处理两个块
            for(start = 0; start <len; start += 2 * block) {
                int low = start;
                int mid = (start + block) < len ? (start + block) : len;
                int high = (start + 2 * block) < len ? (start + 2 * block) : len;
                //两个块的起始下标及结束下标
                int start1 = low, end1 = mid;
                int start2 = mid, end2 = high;
                //开始对两个block进行归并排序
                while (start1 < end1 && start2 < end2) {
                result[low++] = arr[start1] < arr[start2] ? arr[start1++] : arr[start2++];
                }
                while(start1 < end1) {
                result[low++] = arr[start1++];
                }
                while(start2 < end2) {
                result[low++] = arr[start2++];
                }
            }
         //每次归并后把结果result存入arr中，以便进行下次归并
        int[] temp = arr;
        arr = result;
        result = temp;
        }
}
```

---

**递归实现的实现原理**

假设序列共有n个元素

1. 将序列每相邻两个数字进行归并操作，形成floor\(n/2\)个序列，排序后每个序列包含两个元素。

2. 将上述序列再次归并，形成floor\(n/4\)个序列，每个序列包含四个元素

3. 重复步骤2，直到所有元素排序完毕

**Java代码**

```java
public static void main(String[] args) {
        int [] arr ={6,5,3,1,8,7,2,4};
        int len = arr.length;
        int[] reg = new int[len];
        merge_sort_recursive(arr,reg,0,len-1);
        for(int i : arr){
            System.out.println(i);
        }
    }
static void merge_sort_recursive(int[] arr, int[] reg, int start, int end) {
        if (start >= end)
            return;
        int len = end - start, mid = (len >> 1) + start;
        int start1 = start, end1 = mid;
        int start2 = mid + 1, end2 = end;
        //递归到子序列只有一个数的时候，开始逐个返回
        merge_sort_recursive(arr, reg, start1, end1);
        merge_sort_recursive(arr, reg, start2, end2);       
        //-------合并操作，必须在递归之后（子序列有序的基础上）----
        int k = start;
        while (start1 <= end1 && start2 <= end2)
reg[k++] = arr[start1] < arr[start2] ? arr[start1++] : arr[start2++];
        while (start1 <= end1)
            reg[k++] = arr[start1++];
        while (start2 <= end2)
            reg[k++] = arr[start2++];

        //借用reg数组做合并，然后把数据存回arr中
        for (k = start; k <= end; k++)
            arr[k] = reg[k];
}
```



