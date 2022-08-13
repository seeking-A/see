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

### 1. 插入排序

#### 直接插入

#### 希尔排序

| 平均时间 O(nlogn) | 最差时间O(n^s)1<s<2 | 不稳定 | 额外空间O(1) | s是所选分组 |
| ----------------- | ------------------- | ------ | ------------ | ----------- |

基本思想：把数组分为两个数组，前面的有序数组，后边的无序数组，将后边的无序数组的值插入到前边有序数组中。

```java
public static void insertSort(int[] arr){
    /**
     * 把数组分为两个数组，前面的有序数组，后边的无序数组，将后边的无序数组的值插入到前边有序数组中
     */
    for (int i = 1; i < arr.length; i++) {
           int num=arr[i];
           int index=i-1;
           while (index>=0&&num<arr[index]){
               arr[index+1]=arr[index];
               index--;
           }
           if (index+1!=i){
               //因为for循环最后一步index--会比预期index小1
               arr[index+1]=num;
           }
    }
    System.out.println("insert排序后："+Arrays.toString(arr));
}
```



### 2.选择排序

#### 简单选择排序

#### 堆排序





### 3. 交换排序

#### 冒泡排序

| 平均时间 O(n^2) | 最差时间O(n^2) | 稳定 | 额外空间O(1) | n小时较好 |
| --------------- | -------------- | ---- | ------------ | --------- |

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

| 平均时间 O(nlogn) | 最差时间O(n^2) | 不稳定 | 额外空间O(nlogn) | n大时较好 |
| ----------------- | -------------- | ------ | ---------------- | --------- |

基本思想：任意取数组中一个值作为基数，指定两个指针，分别指向数组最左和最右元素，然后两指针向中间移动，使其所指元素与基数比较，然后进行插入排序，排序后折半缩小数组重复之前的排序操作。

```java
/** 
 * 快速排序思想
 *     任意取数组中一个值作为基数，指定两个指针，分别指向数组最左和最右元素，
 *     然后两指针向中间移动，使其所指元素与基数比较，然后进行插入排序，排序后折半缩小数组重复之前的排序操作
 */
public static void quickSort(int[] arr,int left,int right){
    if (left<right) {
        int l = left, r = right + 1;
        int povit = arr[left];

        while (true){
            while (l<right&&arr[++l]<=povit)
                ;
            while (r>left&&arr[--r]>=povit)
                ;
            if (l<r){
                swap(arr,r,l);
            }
            break;
        }
        swap(arr,left,r);
        System.out.println("快速排序后："+Arrays.toString(arr));

        quickSort(arr,left,r-1);
        quickSort(arr,l+1,right);
    }
}


public static void swap(int[] arr,int left,int right){
    int temp = arr[right];
    arr[right] = arr[left];
    arr[left] = temp;
}
```



### 4. 归并排序



### 5. 基数排序

| 平均时间 O(n^2) | 最差时间O(n^2) | 稳定 | 额外空间O(1) | n小时较好 |
| --------------- | -------------- | ---- | ------------ | --------- |

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
num & 1 != 0
```

