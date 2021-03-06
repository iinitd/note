# 排序

## 插入排序

插入排序的基本操作就是将一个数据插入到已经排好序的有序数据中，从而得到一个新的、个数加一的有序数据，算法适用于少量数据的排序，时间复杂度为O\(n^2\)。是稳定的排序方法。插入算法把要排序的数组分成两部分：第一部分包含了这个数组的所有元素，但将最后一个元素除外（让数组多一个空间才有插入的位置），而第二部分就只包含这一个元素（即待插入元素）。在第一部分排序完成后，再将这个最后元素插入到已排好序的第一部分中。

```text
def insert_sort(lists):
    for x in range(1,len(lists)):
        for i in range(x-1, -1, -1): 
           if lists[i] > lists[i+1]:
                temp = lists[i+1]
               lists[i+1] = lists[i]
                lists[i] = temp

    return lists
```

## 希尔排序

希尔排序\(Shell Sort\)是插入排序的一种。也称缩小增量排序，是直接插入排序算法的一种更高效的改进版本。希尔排序是非稳定排序算法。该方法因DL．Shell于1959年提出而得名。 希尔排序是把记录按下标的一定增量分组，对每组使用直接插入排序算法排序；随着增量逐渐减少，每组包含的关键词越来越多，当增量减至1时，整个文件恰被分成一组，算法便终止。

```text
def shell_sort(lists):
    # 希尔排序
    gap = int(len(lists)/2)
    while gap >= 1:
        for x in range(gap, len(lists)):
            for i in range(x-gap, -1, gap):
                if lists[i] > lists[i+gap]:
                    temp = lists[i+gap]
                    lists[i+gap] = lists[i]
                    lists[i] = temp

        gap = int(gap/2)
    returnlists
```

## 冒泡排序

它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。

```text
def bubble_sort(lists):
    # 冒泡排序

    forxinrange(len(lists)):
        foriinrange(x+1, len(lists)):
            iflists[x]>lists[i]:
                lists[x],lists[i]=lists[i],lists[x]
    returnlists
```

## 快速排序

通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

```text
def quick_sort(lists,_left, _right):
    # 快速排序
    if _left>=_right:
        returnlists

    left =_left
    right=_right
    key = lists[left]
    whileleft!=right:
        whileright > leftand lists[right]>=key:
            right-=1
        lists[left]=lists[right]
        whileright > leftandlists[left]<=key:
            left+=1
        lists[right]=lists[left]
    lists[right]=key
    quick_sort(lists, _left,left-1)
    quick_sort(lists, right +1, _right)

    returnlists
```

## 选择排序

基本思想：第1趟，在待排序记录r1 ~ r\[n\]中选出最小的记录，将它与r1交换；第2趟，在待排序记录r2 ~ r\[n\]中选出最小的记录，将它与r2交换；以此类推，第i趟在待排序记录r\[i\] ~ r\[n\]中选出最小的记录，将它与r\[i\]交换，使有序序列不断增长直到全部排序完毕。

```text
def select_sort(lists):
    # 选择排序

    forxinrange(len(lists)):
        min=lists[x]
        foriinrange(x+1, len(lists)):
            ifmin>lists[i]:
                temp = lists[i]
                lists[i] = min
                min = temp

       lists[x] = min

    returnlists
```

## 堆排序

堆排序\(Heapsort\)是指利用堆积树（堆）这种数据结构所设计的一种排序算法，它是选择排序的一种。可以利用数组的特点快速定位指定索引的元素。堆分为大根堆和小根堆，是完全二叉树。大根堆的要求是每个节点的值都不大于其父节点的值，即A\[PARENT\[i\]\] &gt;= A\[i\]。在数组的非降序排序中，需要使用的就是大根堆，因为根据大根堆的要求可知，最大的值一定在堆顶。

```text
def adjust_heap(lists,i,size):
    lchild=2*i
    rchild=2*i+1`
    max=i
    ifi<size/2:
        iflchild<sizeandlists[lchild]>lists[max]:
            max=lchild
        ifrchild<sizeandlists[rchild]>lists[max]:
            max=rchild
        ifmax!=i:
            lists[max],lists[i]=lists[i],lists[max]
            adjust_heap(lists,max,size)

def build_heap(lists,size):
    foriinrange(0,(size/2))[::-1]:
        adjust_heap(lists,i,size)

def heap_sort(lists):
    size=len(lists)
    build_heap(lists,size)
    foriinrange(0,size)[::-1]:
        lists[0],lists[i]=lists[i],lists[0]
        adjust_heap(lists,0,i)
```

## 归并排序

归并排序是建立在归并操作上的一种有效的排序算法,该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为二路归并。

归并过程为：比较a\[i\]和a\[j\]的大小，若a\[i\]≤a\[j\]，则将第一个有序表中的元素a\[i\]复制到r\[k\]中，并令i和k分别加上1；否则将第二个有序表中的元素a\[j\]复制到r\[k\]中，并令j和k分别加上1，如此循环下去，直到其中一个有序表取完，然后再将另一个有序表中剩余的元素复制到r中从下标k到下标t的单元。归并排序的算法我们通常用递归实现，先把待排序区间\[s,t\]以中点二分，接着把左边子区间排序，再把右边子区间排序，最后把左区间和右区间用一次归并操作合并成有序的区间\[s,t\]。

```text
def merge(left,right):
    i,j=0,0
    result=[]
    whilei<len(left)andj<len(right):
        ifleft[i]<=right[j]:
            result.append(left[i])
            i+=1
        else:
            result.append(right[j])
            j+=1
    result+=left[i:]
    result+=right[j:]
    returnresult

defmerge_sort(lists):
    # 归并排序
    iflen(lists)<=1:
        returnlists
    num=len(lists)/2
    left=merge_sort(lists[:num])
    right=merge_sort(lists[num:])
    returnmerge(left,right)
```

