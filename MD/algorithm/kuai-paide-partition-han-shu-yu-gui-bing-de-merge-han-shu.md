> 快排的partition函数
>
> 作用：给定一个数组arr\[\]和数组中任意一个元素a，重排数组使得a左边都小于它，右边都不小于它

```java
// A[]为数组，start、end分别为数组第一个元素和最后一个元素的索引
// povitIndex为数组中任意选中的数的索引
static int partition(int A[], int start, int end, int pivotIndex){
    int i = start, j = end, pivot = A[pivotIndex];
    swap<int>(A[end], A[pivotIndex]);
    while(i < j){
        while(i < j && A[i] <= pivot) ++i;
        while(i < j && A[j] >= pivot) --j;
        if(i < j) swap<int>(A[i], A[j]);
    }
    swap<int>(A[end], A[i]);
    return i;
}
```

> 归并的Merge函数
>
> 思想：分治原则，合的时候进行排序，稳定排序。
>
> 需要用到两个函数，merge函数负责将有序的两个数组进行进行合并，mergeSort函数负责递归实现分组处理。

**Java代码**

```java
public class MergeSort {

    public static void main(String[] args) {
        int a[]={3,2,5,4,7,9};
        int tmp[]=new int[a.length];
        new MergeSort().mergeSort(a, 0, a.length-1, tmp);
        for (int i : a) {
            System.out.println(i);
        }
    }
    //将有序数组a[first..mid] a[mid+1,last]合并
    void merge(int a[],int first,int mid,int last,int tmp[]){
        int i=first;//前一个数组的开始下标
        int j=mid+1;//后一个数组的开始下标
        int m=mid;//前一个数组的最后下标
        int n=last;//后一个数组的最后下标
        int k=0;//存放临时数组到tmp
        while(i<=m&&j<=n){
            if(a[i]<=a[j]){
                tmp[k++]=a[i];
                i++;
            }else {
                tmp[k++]=a[j];
                j++;
            }
        }
        while(i<=m){
            tmp[k++]=a[i++];
        }
        while(j<=n){
            tmp[k++]=a[j++];
        }
        //复制tmp到a数组
        for(i=0;i<k;i++){
            a[first+i]=tmp[i];
        }

    }
    void mergeSort(int a[],int first,int last,int tmp[]){
        if(first<last){
            int mid=(first+last)/2;
            mergeSort(a, first, mid, tmp);//左边有序
            mergeSort(a, mid+1, last, tmp);//右边有序
            merge(a, first, mid, last, tmp);//合并
        }
    }

}
```



