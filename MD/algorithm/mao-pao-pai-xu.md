> 冒泡排序通过重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来，直到没有再需要交换的元素为止对n个项目需要**O\( n2\)**的比较次数。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

**实现步骤**

1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。　

2. 对每一对相邻元素做同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。

3. 针对所有的元素重复以上的步骤，除了最后一个。

4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

**Java实现**

```java
public static void main(String[] args) {
        int[] number = {95,45,15,78,84,51,24,12};
        bubble_sort(number);
        for(int i = 0; i < number.length; i++) {
            System.out.print(number[i] + " ");
            }
    }
    public static void bubble_sort(int[] arr) {
            int  temp, len = arr.length;
            for (int i = 0; i < len - 1; i++)
                for (int j = 0; j < len - 1 - i; j++)
                    if (arr[j] > arr[j + 1]) {
                        temp = arr[j];
                        arr[j] = arr[j + 1];
                        arr[j + 1] = temp;
                }
}
```

  


