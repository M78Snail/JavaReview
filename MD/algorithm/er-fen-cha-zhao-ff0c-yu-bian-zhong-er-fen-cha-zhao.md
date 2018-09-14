### 二分查找

> 二分搜索（binary search），也称折半搜索（half-interval search）、对数搜索（logarithmic search），是一种在有序数组中查找某一特定元素的搜索算法。搜索过程从数组的中间元素开始，如果中间元素正好是要查找的元素，则搜索过程结束；如果某一特定元素大于或者小于中间元素，则在数组大于或小于中间元素的那一半中查找，而且跟开始一样从中间元素开始比较。如果在某一步骤数组为空，则代表找不到。这种搜索算法每一次比较都使搜索范围缩小一半。
>
> 除直接在一个数组中查找元素外，可用在插入排序中。

**复杂度分析**

| ![](/assets/import5.12.png) |
| :---: |


时间复杂度

* 折半搜索每次把搜索区域减少一半，时间复杂度为Ｏ\(Log n\) 。（n代表集合中元素的个数）

* 空间复杂度

| 迭代 | 递归 |
| :---: | :---: |
| O\(1\) | O\(log n\) |

  
**Java实现**

```java
// 递归版本
int binary_search(const int arr[], int start, int end, int key) {
    if (start > end)
        return -1;

    int mid = start + (end - start) / 2; //直接平均可能会溢位
    if (arr[mid] > key)
        return binary_search(arr, start, mid - 1, key);
    if (arr[mid] < key)
        return binary_search(arr, mid + 1, end, key);
    return mid; 
}
```

```java
// while循环
int binary_search(const int arr[], int start, int end, int key) {
    int mid;
    while (start <= end) {
        mid = start + (end - start) / 2; //直接平均可能会溢位
        if (arr[mid] < key)
            start = mid + 1;
        else if (arr[mid] > key)
            end = mid - 1;
        else
            return mid; 
    }
    return -1;
}
```

---

### **变种二分查找**

  
**改变数组**

升序数组a经过循环右移后，二分查找给定元素x的位置。

如a={1,2,3,4,5,6,7}，循环移动后a={5,6,7,1,2,3,4}

**思路**  
移动后的数组分为两部分，两部分内部都是升序排列的，中值mid必然属于这两部分之一。然后根据下标判断查找值是否属于这一部分，循环缩小mid值，直到arr\[mid\]等于查找值就返回。

**Java实现**

```java
public static void main(String[] args){
          int arr[]={5,6,7,1,2,3,4};
          int out = VariantBinaryFind(arr,arr.length-1,1);
          System.out.println("下标为： "+ out);
}
/**
  * @param a  数组
  * @param len 数组长度
  * @param x 查找的元素
  * @return 返回查找值得下标，不存在返回 -1
  */
public static int VariantBinaryFind(int arr[],int len,int x){
          int left = 0;
          int right = len;
          while(left<=right){
              int mid = left + (right -left)/2;
              if(arr[mid]==x){
                  return mid;
              }
              if(arr[mid] >= arr[left]) //mid在左边序列
                {  
                    if(arr[left] > x || arr[mid] < x)  
                        left = mid + 1;  //x在右边序列
                    else  
                        right = mid - 1;  //x在左边序列
                }  
                else //mid在右边序列
                {  
                    if(arr[right] < x || arr[mid] > x)  
                        right = mid - 1;  //x在左边序列
                    else  
                        left = mid + 1;  //x在右边序列
                }  
            }  
     return -1;  
}
```

运行结果：

`下标为：3`

