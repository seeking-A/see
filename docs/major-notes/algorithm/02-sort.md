---
title: 排序算法
tags:
 - 算法
categories:
 - 算法
---

## 常见时间复杂度

- 常数阶 O(1)
- 对数阶 O (log2n)
- 线性阶 O(n)
- 线性对数阶 O(nlog2n)
- 平方阶 O(n^2)
- 立方阶 O(n^3)
- 指数阶 O(2^n)



## 排序算法

| 算法名称 | 平均时间  | 最差时间    | 稳定性 | 额外空间 | 额外说明                            |
| -------- | --------- | ----------- | ------ | -------- | ----------------------------------- |
| 直接插入 | O(n^2)    | O(n^2)      | 稳定   | O(1)     | 大部分元素有序时效率好              |
| 希尔排序 | O(nlogn)  | O(n^s)1<s<2 | 不稳定 | O(1)     | s是所选分组                         |
| 简单选择 | O(n^2)    | O(n^2)      | 不稳定 | O(1)     | n较小时效率好                       |
| 堆排序   | O(nlogn)  | O(nlogn)    | 不稳定 | O(1)     | n较大时效率好                       |
| 冒泡排序 | O(n^2)    | O(n^2)      | 稳定   | O(1)     | n较小时效率好                       |
| 快速排序 | O(nlogn)  | O(n^2)      | 不稳定 | O(nlogn) | n较大时效率好                       |
| 归并排序 | O(nlogn)  | O(nlogn)    | 稳定   | O(1)     | n较大时效率好                       |
| 基数排序 | O(log^RB) | O(log^RB)   | 稳定   | O(n)     | B为真数（0~9）<br>R是基数（个十百） |



### 1. 插入排序

#### 直接插入

基本思想：把数组分为两个数组，前面的有序数组，后边的无序数组，将后边的无序数组的值插入到前边有序数组中。

```java
public static void insertSort(int[] arr){
    /**
     * 把数组分为两个数组，前面的有序数组，后边的无序数组，将后边的无序数组的值插入到前边有序数组中
     */
    for (int i = 1; i < arr.length; i++) {
           int num=arr[i], index= i;
           while (index>=0&&num<arr[index-1]){
               arr[index]=arr[index-1];
               index--;
           }
           arr[index] = num;
    }
    System.out.println("insert排序后："+Arrays.toString(arr));
}
```



#### 希尔排序



### 2.选择排序

#### 简单选择排序

#### 堆排序

```java
public void heapSort(int[] arr){
    int size = arr.length;
    buildHeap(arr,size);
    for(int i=0;i<size;i++){
        swap(arr, 0, size-i-1);
        modHeap(arr, i, size);
    }
}

private void buildHeap(int[] arr, int heapSize){
    for(int i=(heapSize-2)>>1;i>=0;i++){
        modHeap(arr, i, heapSize);
    }
}

private void modHeap(int[] arr, int root, int size){
    int left = root * 2 +1;
    int right = root * 2 + 1;
    int index = root;
    if(left <root&& arr[left]>arr[root]){
        root = left;
    }
    if(left>root&&arr[right]<arr[root]){
        root = right;
    }
    if(index!=root){
        swap(arr, root, index);
        modHeap(arr, root, size);
    }
}
```





### 3. 交换排序

#### 冒泡排序

基本思想：指定两个相邻指针在数组中移动，相邻指针所指两个元素进行比较交换。

```java
/**
 * 冒泡排序思想
 *     指定两个相邻指针在数组中移动，相邻指针所指两个元素进行比较交换
 */
public static void bubbleSort(int[] arr){
    for (int i = 0; i < arr.length-1; i++) {
        int left=0,right=1;
        boolean flag=false;//用于判断是否进行过交换
        for (int j = 0; j < arr.length-1-i; j++) {
            if (arr[right]<arr[left]){
                flag=true;
                swap(arr,left,right);
            }
            left++;
            right++;
        }

        if (!flag){
            break;
        }else {
            flag=false;
        }
    }
    System.out.println("冒泡排序后："+Arrays.toString(arr));
}
```



#### 快速排序

基本思想：任意取数组中一个值作为基数，指定两个指针，分别指向数组最左和最右元素，然后两指针向中间移动，使其所指元素与基数比较，然后进行插入排序，排序后折半缩小数组重复之前的排序操作。

```java
/** 
 * 快速排序思想
 *     任意取数组中一个值作为基数，指定两个指针，分别指向数组最左和最右元素，
 *     然后两指针向中间移动，使其所指元素与基数比较，然后进行插入排序，排序后折半缩小数组重复之前的排序操作
 */
public void quickSort(int[] arr, int start, int end) {
    if (start<end){
        int pov = partition(arr,start,end);
        quickSort(arr, start,pov-1);
        quickSort(arr,pov+1,end);
    }
}

private int partition(int[] arr, int start, int end) {
    int pov = arr[start];
    while (start<end){
        while (start<end&& arr[end]>=pov){
            end--;
        }
        swap(arr,start,end);
        while (start<end&& arr[start]<=pov){
            start++;
        }
        swap(arr,start,end);
    }
    return start;
}

public static void swap(int[] arr,int left,int right){
    int temp = arr[right];
    arr[right] = arr[left];
    arr[left] = temp;
}
```



### 4. 归并排序

```java
public void mergeSort(int[] arr, int left, int right) {
    if (left < right) {
        int mid = (left + right) >> 1;
        mergeSort(arr, left, mid);
        mergeSort(arr, mid + 1, right);
        merge(arr, left, mid, right);
    }
}

private void merge(int[] arr, int left, int mid, int right) {
    int[] tmp = new int[arr.length];
    int i = left, l = left, r = mid + 1;
    while (l <= mid && r <= right) {
        tmp[i++] = arr[l] <= arr[r] ? arr[l++] : arr[r++];
    }
    while (r <= right)
        tmp[i++] = arr[r++];
    while (l <= mid)
        tmp[i++] = arr[l++];
    while (left <= right)
        arr[left] = tmp[left++];
}
```





### 5. 基数排序

基本思想： 将所有待比较数值统一为同样的数位长度，数位较短的数前面补零。然后，从最低位开始，依次进行一次排序。

```java
/**
 * 基数排序法思想：
 *      将所有待比较数值统一为同样的数位长度，数位较短的数前面补零。然后，从最低位开始，依次进行一次排序
 */
public static void radixSort(int[] arr){
    int[][] bucket=new int[10][arr.length];//创建10个桶
    int[] bucketElementCounts=new int[10];//用于记录每个桶存放的元素个数

    //得到数组中最大的数的位数
    int max=arr[0];
    for (int i = 0; i < arr.length; i++) {
        if (arr[i]>max){
            max=arr[i];
        }
    }
    int maxLen=(max+"").length();

    for (int k = 0,n=1; k < maxLen; k++,n*=10) {
        //1.将数组中元素按低位元素大小放入桶中
        for (int i = 0; i < arr.length; i++) {
            int value=arr[i]/n%10;
            //bucketElementCounts[value]表示该桶中存放的元素个数
            bucket[value][bucketElementCounts[value]]=arr[i];
            bucketElementCounts[value]++;
        }
        //2.将桶中元素取出，放回到原来数组中
        int index=0;
        for (int i = 0; i < bucketElementCounts.length; i++) {
            if (bucketElementCounts[i]!=0){
                for (int l = 0; l < bucketElementCounts[i]; l++) {
                    arr[index++]=bucket[i][l];
                }
            }
            bucketElementCounts[i]=0;
        }
    }
    System.out.println("基数排序后："+Arrays.toString(arr));
}
```



## 技巧

### 求中间值

```java
int mid = L+ (R-L)>>1;
int mid = L + (R-L)/2;
```

### 判断一个数是否为偶数

```java
num & 1 == 0;//true为偶数，false为奇数
```

