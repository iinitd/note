# 二分查找

在计算机科学中，二分搜索（binary search），也称折半搜索（half-interval search）、对数搜索（logarithmic search），是一种在有序数组中查找某一特定元素的搜索算法。搜索过程从数组的中间元素开始，如果中间元素正好是要查找的元素，则搜索过程结束；如果某一特定元素大于或者小于中间元素，则在数组大于或小于中间元素的那一半中查找，而且跟开始一样从中间元素开始比较。如果在某一步骤数组为空，则代表找不到。这种搜索算法每一次比较都使搜索范围缩小一半。

问题1 给定一个有序的数组，查找value是否在数组中，不存在返回-1。

```text
int BinarySearch(int array[], int n, int value)
{
    int left = 0;
    int right = n - 1;
    //如果这里是int right = n 的话，那么下面有两处地方需要修改，以保证一一对应：
    //1、下面循环的条件则是while(left < right)
    //2、循环内当 array[middle] > value 的时候，right = mid

    while (left <= right)  //循环条件，适时而变
    {
        int middle = left + ((right - left) >> 1);  //防止溢出，移位也更高效。同时，每次循环都需要更新。
        if (array[middle] > value)
            right = middle - 1;  //right赋值，适时而变
        else if (array[middle] < value)
            left = middle + 1;
        else
            return middle;
        //可能会有读者认为刚开始时就要判断相等，但毕竟数组中不相等的情况更多
        //如果每次循环都判断一下是否相等，将耗费时间
    }
    return -1;
}
```

问题2 给定一个有序的数组，查找value第一次出现的下标，不存在返回-1。

```text
int BinarySearch(int array[], int n, int value)
{
    int left = 0;
    int right = n - 1;

    while (left <= right)  
    {
        int middle = left + ((right - left) >> 1);
        if (array[middle] >= value)  //因为是找到最小的等值下标，所以等于号放在这里
            right = middle - 1;
        else
            left = middle + 1;
    }
    return array[right + 1] == value ? right + 1 : -1;
}
```

如果问题改为查找value最后一次出现的下标呢？只需改动两个位置： 1.if \(array\[middle\] &gt;= value\)中的等号去掉； 2.return中right+1改为left-1。

问题3 给定一个有序的数组，查找最接近value且大于value的数的下标（如果该数存在多个，返回第一个下标），不存在返回-1。

```text
int BinarySearch(int array[], int n, int value)
{
    int left = 0;
    int right = n - 1;

    while (left <= right)  
    {
        int middle = left + ((right - left) >> 1);
        if (array[middle] > value)
            right = middle - 1;
        else
            left = middle + 1;
    }
    return array[right + 1] > value ? right + 1 : -1;
}
```

如果问题改为查找最接近value且小于value的数的下标（如果该数存在多个，返回最后一个下标）呢？只需改动两个位置： 1.if \(array\[middle\] &gt; value\)加入一个等号； 2.return array\[right + 1\] &gt; value ? right + 1 : -1;改为return array\[left-1\] &lt; value ? left - 1 : -1;。

总结 二分算法所操作的区间，是左闭右开，还是左闭右闭，需要在循环体跳出判断中，以及每次修改left,，right区间值这两个地方保持一致，否则就可能出错。

