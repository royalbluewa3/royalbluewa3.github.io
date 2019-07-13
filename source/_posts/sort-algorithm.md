---
title: 常见排序算法总结（javascript实现）
categories: FE
tags: algorithm
abbrlink: 67d771c4
date: 2019-07-12 16:18:13
---


学习算法知识，先从基础的排序问题入手。本文分析了常见排序算法的思路并通过实例分步说明，算法流程大都均配合动画演示，最后给出 JavaScript 的实现方法。
文章中难免出现某些错误，欢迎指正。

<br/> 

## 冒泡排序 (Bubble Sort)

冒泡排序是一种简单的排序算法，其得名于较小的元素会经由反复交换逐渐“浮”至数列顶端。

### 算法思路
1. 从第一个元素开始，比较两相邻元素。若前者大于后者，则两者互换。
2. 一次外循环结束，末端元素为最大的数，之后不再参与排序。
3. 对剩余每对相邻元素重复上述步骤，直至再无任何一对数据需比较。
<!-- more -->

### 实例演示
以数组 `[5, 9, 3, 1, 10, 6]` 为例（未发生交换的步骤已省略）：

![Bubble_Sort.gif](https://i.loli.net/2018/06/02/5b12889f0302c.gif)
```
第一次循环：
   [5, 9, 3, 1, 10, 6] → 
   [5, 3, 9, 1, 10, 6] → 
   [5, 3, 1, 9, 10, 6] →
   [5, 3, 1, 9, 6, 10]  

第二次循环（除开末端元素 10，对剩余元素排序）：
→ [3, 5, 1, 9, 6, 10] → 
   [3, 1, 5, 9, 6, 10] → 
   [3, 1, 5, 6, 9, 10]

第三次循环（除开后两个已排序好的元素）：
→ [1, 3, 5, 6, 9, 10] →
   [1, 3, 5, 6, 9, 10]

第四次循环（对前三个元素排序，可注意到数组已排序完成，但程序本身并不知道，因此后续循环将继续进行）：
→ [1, 3, 5, 6, 9, 10]

第五次循环（最后一次）
```

### JS实现

```javascript
function bubbleSort(arr) {
   var len = arr.length,
       i,
       j,
       temp;
   for (i = len - 1; i > 0; i--) {
       for (j = 0; j < i; j++) {
           if (arr[j] > arr[j + 1]) {
               temp = arr[j];
               arr[j] = arr[j + 1];
               arr[j + 1] = temp
           }
       }
   }
   return arr;
}
```


## 鸡尾酒排序 (Cocktail Sort)

鸡尾酒排序，又称双向冒泡排序，是冒泡排序的轻微变形。不同在于，鸡尾酒排序是从低到高然后从高到低来回排序的，而冒泡排序仅从低到高排序。鸡尾酒排序可能比冒泡排序的效率稍好一点，原因是冒泡排序仅从一个方向进行比对，每次循环只移动一个项目。但在乱数序列状态下，鸡尾酒排序与冒泡排序的效率都很低，优点只有原理简单这一点。

### 算法思路
1. 先对数组从左到右进行冒泡排序（升序），最大的元素移至最右端。
2. 再对数组从右到左进行冒泡排序（降序），最小的元素移至最左端。
3. 以此类推，依次改变冒泡的方向，并不断缩小未排序元素的范围，直到最后一个元素结束。

### 实例演示
仍以数组 `[5, 9, 3, 1, 10, 6]` 为例：
```
第一次循环：
   [5, 9, 3, 1, 10, 6] → 
   [5, 3, 9, 1, 10, 6] → 
   [5, 3, 1, 9, 10, 6] →
   [5, 3, 1, 9, 6, 10] →
   [5, 3, 1, 6, 9, 10] →
   [5, 1, 3, 6, 9, 10] →
   [1, 5, 3, 6, 9, 10] →

第二次循环（最后一次）：
→ [1, 3, 5, 6, 9, 10]
```

### JS实现

```javascript
function cocktailSort(arr) {
    function swap(arr, i, j) {
        var temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
    var len = arr.length,
        left = 0,
        right = len - 1,
        lastSwappedLeft = left,
        lastSwappedRight = right,
        i,
        j;
    while (left < right) {
        // 从左到右
        lastSwappedRight = 0;
        for (i = left; i < right; i++) {
            if (arr[i] > arr[i + 1]) {
                swap(arr, i, i + 1);
                lastSwappedRight = i;
            }
        }
        right = lastSwappedRight;
        // 从右到左
        lastSwappedLeft = len - 1;
        for (j = right; left < j; j--) {
            if (arr[j - 1] > arr[j]) {
                swap(arr, j - 1, j)
                lastSwappedLeft = j
            }
        }
        left = lastSwappedLeft;
    }
}
```


## 插入排序 (Insertion Sort)

插入排序是一种简单直观的排序算法。它的原理是构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。在实现上，插入排序通常采用 `in-place` 排序，因而在从后向前扫描过程中，需反复把已排序元素逐步向后挪位，为最新元素提供插入空间。

当需要排序的数据量很小，例如，量级小于千，那么插入排序还是一个不错的选择。

插入排序有一种优化算法，叫 **二分（折半）插入排序(Binary insert sort)** 。其与直接插入排序最大的区别在于查找插入位置时使用二分查找的方式，在速度上有一定提升。这里不详细说明。

### 算法思路
1. 第一个元素作为起始元素，默认已被排序。
2. 按顺序取出之后的一个新元素，在已被排序的元素中从后向前扫描，
如新元素小于被排序的某元素，则将该被比较的已排序元素移到下一位置。
3. 对新元素和已排序元素重复上述比较步骤，直至存在某已排序元素小于或等于新元素，然后将新元素插至该已排序元素的位置后。
4. 重复步骤 `2~3`。

### 实例演示
以数组 `[5, 9, 3, 1, 10, 6, 7, 2]` 为例：

![Insertion_Sort.gif](https://i.loli.net/2018/06/02/5b1288d564ea4.gif)
```
[5]  9  3  1  10  6  7  2
[5, 9]  3  1  10  6  7  2
[3, 5, 9]  1  10  6  7  2
[1, 3, 5, 9]  10  6  7  2
[1, 3, 5, 9, 10]  6  7  2
[1, 3, 5, 6, 9, 10]  7  2
[1, 3, 5, 6, 7, 9, 10]  2
[1, 2, 3, 5, 6, 7, 9, 10]
```

### JS实现

```javascript
function insertSort(arr){
    var temp;
    for(var i=1;i<arr.length;i++){
        temp  = arr[i];
        for(var j=i;j>=0;j--){
            if(arr[j-1]>temp){
                arr[j]=arr[j-1];
            }else{
                arr[j]=temp;
                break;
            }
        }
    }
    return arr
}
```


## 希尔排序 (Shell Sort)

希尔排序，也称递减增量排序算法，是插入排序的一种高速而稳定的改进版本。与插入排序的不同之处在于，它会优先比较距离较远的元素。在初期选用增量较大的间隔，使数据跳跃式接近其位置，然后增量逐渐缩小直至增量为 `1`。这样移动次数可大大减少，提高排序效率。
希尔排序的核心在于间隔序列的设定。既可以提前设定好间隔序列，也可以动态定义。

### 算法思路
1.取一个正整数 `d1(d1 < n)`，把全部数据分为 d1 个组，所有距离为 `d1` 的倍数的数据视为一组，然后在各组内进行插入排序。
2. 取正整数 `d2(d2 < d1)`。
3. 重复上述分组和排序操作，直到取 `di = 1(i >= 1)` ，对这个组插入排序。一般选 `d1`  约为 `n/2`，`d2` 为 `d1/2`， `d3` 为 `d2/2`，`…`， `di = 1`。

### 实例演示
以数组 `[82, 96, 63, 17, 42, 30, 68, 76, 11]` 为例，首先取 `d1 = 4`，将数组分为 `4` 组，小组编号`01~04`。
```
01  02  03  04  01  02  03  04  01
 |   |   |   |   |   |   |   |   |
82  96  63  17  42  30  68  76  11

然后分别对 4 个小组进行插入排序，排序后的结果为：
 |   |   |   |   |   |   |   |   |
11  30  36  17  42  96  68  76  82

取 d2 = 2，将数组分为 2 个小组，然后分别对 2 个小组进行插入排序，排序后的结果为：

01  02  01  02  01  02  01  02  01
 |   |   |   |   |   |   |   |   |
11  30  36  17  42  96  68  76  82

最后，取 d3 = 1，插入排序后得到最终结果：
[11  17  30  36  42  68  76  82  96]
```

### JS实现

```javascript
function shellSort(arr) {
    var len = arr.length,
        temp,
        gap = 1;
    while(gap < len/3) {          //动态定义间隔序列
        gap =gap*3+1;
    }
    for (gap; gap > 0; gap = Math.floor(gap/3)) {
        for (var i = gap; i < len; i++) {
            temp = arr[i];
            for (var j = i-gap; j >= 0 && arr[j] > temp; j-=gap) {
                arr[j+gap] = arr[j];
            }
            arr[j+gap] = temp;
        }
    }
    return arr;
}
```


## 选择排序 (Selection Sort)

选择排序是一种相当直观的，完全依靠交换去移动元素的排序算法。唯一优点不占用额外内存空间。

### 算法思路
1. 将数组中第一个元素设为初始最小值。
2. 遍历每个未排序元素，找出剩余序列中元素最小值，与初始最小值比较，若该元素值小于初始最小值，则与初始最小值交换位置并将其设为新最小值；若该元素值大于或等于初始最小值，则位置不移动。
3. 在剩余未排序元素中重复步骤 `2`，直至所有元素排序完毕。

### 实例演示
以数组 `[5, 9, 3, 1, 10, 6, 7, 2, 12, 8]` 为例：

![Selection_Sort.gif](https://i.loli.net/2018/06/02/5b12897ac09a7.gif)
```
首先设 5 为初始最小值，再从数组中找到最小的数 1，与 5 交换位置：
[5, 9, 3, 1, 10, 6, 7, 2, 12, 8] →
[1, 9, 3, 5, 10, 6, 7, 2, 12, 8]

在剩余序列中找到最小的数 2，与剩余序列首元素交换位置：
[1, 9, 3, 5, 10, 6, 7, 2, 12, 8] →
[1, 2, 3, 5, 10, 6, 7, 9, 12, 8]

继续在剩余序列中找到最小数 3，与剩余序列首元素交换位置（实际上不需要交换，过程省略）：
[1, 2, 3, 5, 10, 6, 7, 9, 12, 8] 

重复上述过程，直至所有元素排序完毕：
[1, 2, 3, 5, 10, 6, 7, 9, 12, 8] →
[1, 2, 3, 5, 6, 10, 7, 9, 12, 8] →
[1, 2, 3, 5, 6, 7, 10, 9, 12, 8] →
[1, 2, 3, 5, 6, 7, 8, 9, 12, 10] →
[1, 2, 3, 5, 6, 7, 8, 9, 12, 10] →
[1, 2, 3, 5, 6, 7, 8, 9, 10, 12]
```

### JS实现

```javascript
function selectionSort(arr) {
  var len = arr.length,
      i,
      j,
      minIndex,
      minValue,
      temp;
  for (i = 0; i < len - 1; i++) {
    minIndex = i;
    minValue = arr[minIndex];
    for (j = i + 1; j < len; j++) { //通过循环选出最小的
      if (arr[j] < minValue) {
        minIndex = j;
        minValue = arr[minIndex];
      }
    }
    // 交换位置
    temp = arr[i];
    arr[i] = minValue;
    arr[minIndex] = temp;
  }
  return arr
}
```


## 归并排序 (Merge Sort)

归并排序是建立在归并操作上的一种有效的排序算法，该算法是采用分治法(Divide and Conquer)的典型应用。和选择排序一样，归并排序的性能不受输入数据的影响，但表现比选择排序好的多，代价是需要额外的内存空间。

归并排序每次都在相邻的数据中进行操作，所以其在 `O(nlogn)` 的几种排序方法（希尔排序，归并排序，快速排序，堆排序）中效率也是比较高的。

>**分治法的基本思想：**
将原问题分解为若干个规模更小但结构与原问题相似的子问题。递归地解这些子问题，然后将这些子问题的解组合为原问题的解。

归并操作(Merge)，也叫归并算法，指的是将两个已经排序的序列合并成一个序列的操作。归并排序算法依赖归并操作。
归并排序有多路归并排序、两路归并排序，可用于内排序，也可用于外排序。这里仅对内排序的两路归并方法进行讨论。

### 算法思路
1. 把原数组分拆为 `n` 个长度为 `1` 的子数组。
2. 将长度为 `1` 的子数组两两归并为 `n/2` 个长度为 `2` 的子数组，并使每个子数组有序。
3. 重复步骤 `2` 直至所有子数组归并为长度为 `n` 的有序数组为止。

### 实例演示
以数组 `[5, 9, 3, 1, 10, 6, 7, 2]` 为例：

![Merge_Sort.gif](https://i.loli.net/2018/06/02/5b1288ee14d03.gif)
```
[5, 9, 3, 1, 10, 6, 7, 2] →
[5]   [9]   [3]   [1]   [10]   [6]   [7]   [2] →
[5, 9]   [3, 1]   [10, 6]   [7, 2] →
[5, 9]   [1, 3]   [6, 10]   [2, 7] →
[5, 9, 1, 3]   [6, 10, 2, 7] →
[1, 3, 5, 9]   [2, 6, 7, 10] →
[1, 3, 5, 9, 2, 6, 7, 10] →
[1, 2, 3, 5, 6, 7, 9, 10]
```

### JS实现

```javascript
function mergeSort(arr) {
    function sort(arr, first, last) {
        first = (first === undefined) ? 0 : first
        last = (last === undefined) ? arr.length - 1 : last
        if (last - first < 1) {
            return;
        }
        var middle = Math.floor((first + last) / 2);
        sort(arr, first, middle);
        sort(arr, middle + 1, last);
        var f = first,
            m = middle,
            i,
            temp;
        while (f <= m && m + 1 <= last) {
            if (arr[f] >= arr[m + 1]) { // 这里使用了插入排序的思想
                temp = arr[m + 1];
                for (i = m; i >= f; i--) {
                    arr[i + 1] = arr[i];
                }
                arr[f] = temp;
                m++
            } else {
                f++
            }
        }
        return arr;
    }
    return sort(arr);
}
```


## 快速排序 (Quick Sort)

快速排序与归并排序一样，是分治法的典型应用。本质上，快速排序可看作是在冒泡排序基础上的递归分治法。其基本思想是，通过一次排序将待排数据分隔成独立的两部分，其中一部分数据的关键字均比另一部分的关键字小，则可分别对这两部分数据继续进行排序，以达到整个序列有序。

它是处理大数据最快的排序算法之一。虽然快速排序 Worst Case 的时间复杂度达到了 `O(n²)`，比如说顺序数列的快排。但它的期望时间是 `O(nlogn)`，且`O(nlogn)` 记号中隐含的常数因子很小，比复杂度稳定等于 `O(nlogn)` 的归并排序要小很多。所以对绝大多数顺序性较弱的随机数列而言，快速排序总是优于归并排序。

### 算法思路
1. 从数组中选择一个元素作为”基准”（pivot）。
2. 所有小于 `pivot` 的元素，都移其左侧至；所有大于 `pivot` 的元素，都移到其右侧，此操作称为分区 (partition)。一次分区操作结束后，`pivot` 所处的位置就是最终排序后它的位置。
3. 对 `pivot` 左右两侧子集，不断重复步骤 `1~2`，直至所有子集只剩下一个元素为止。

### 实例演示
以数组 `[5, 2, 7, 6, 10, 3, 8, 9]` 为例：

![Quick_Sort.gif](https://i.loli.net/2018/06/02/5b1289368e09a.gif)
```
首先选定一个基准元素，这里我们选取 6 为pivot（可任意选择）：

              pivot
                |
    5   2   7   6   10   3   8   9


将基准元素与数组最后一个元素交换位置，若选择末端元素为基准元素则可省略该步：

                               pivot
                                 |
    5   2   7   9   10   3   8   6


从左至右遍历（除了最后的pivot），所有小于 6 的元素与 storeIndex 所指向的元素交换位置。


i == 0，storeIndex == 0，找到小于pivot的元素 5，与 storeIndex 所在位置的元素交换位置，这里为 5 本身：

                               pivot
                                 |
    5   2   7   9   10   3   8   6
    |
storeIndex

一次交换后 storeIndex + 1，storeIndex == 1；


i == 1，storeIndex == 1，找到小于pivot的元素 2，与 storeIndex 所在位置的元素交换位置，这里是 2 本身：

                               pivot
                                 |
    5   2   7   9   10   3   8   6
        |
    storeIndex

一次交换后 storeIndex == 2；


i == 5，storeIndex == 2，找到小于pivot的元素 3，与 storeIndex 所在位置的元素交换位置：

                               pivot
                                 |
    5   2   7   9   10   3   8   6   →   
            |            |
        storeIndex       i

                               pivot
                                 |
    5   2   3   9   10   7   8   6      
                |
            storeIndex

一次交换后 storeIndex == 3；


循环结束，交换 pivot 与 storeIndex 指向元素的位置：

              pivot
                |
    5   2   3   6   10   7   8   9      
                |
            storeIndex
```
如此完成一次分区操作，此时 `storeIndex` 对应位置就是 `pivot` 的最终位置，接下来对 `pivot` 左右两侧子集执行相同操作至单元素子集即可。

### JS实现

这里推荐原地(in-place)分区版本的快速排序。关于原地算法可参考[In-place algorithm](https://en.wikipedia.org/wiki/In-place_algorithm)
```javascript
function quickSort(arr) {
    // 交换
    function swap(arr, a, b) {
        var temp = arr[a];
        arr[a] = arr[b];
        arr[b] = temp;
    }
    // 分区
    function partition(arr, left, right) {
        /**
         * 开始时并不知道 pivot 的最终存放位置，故可先将 pivot 交换到后面去，
         * 这里直接定义最右边的元素为基准
         */
        var pivot = arr[right];
        /**
         * 存放小于 pivot 的元素时，是紧挨着上一元素的，否则空隙里存放的可能是大于pivot的元素，
         * 故声明变量 storeIndex，并初始化为left来依次紧挨着存放小于 pivot 的元素
         */
        var storeIndex = left;
        for (var i = left; i < right; i++) {
            if (arr[i] < pivot) {
                /**
                 * 遍历数组，找到小于 pivot 的元素，（大于 pivot 的元素会跳过）
                 * 将循环 i 次时得到的元素，通过 swap 交换放到 storeIndex 处，
                 * 并对 storeIndex 递增 1，表示下一个可能要交换的位置
                 */
                swap(arr, storeIndex, i);
                storeIndex++;
            }
        }
        // 最后： 将 pivot 交换到 storeIndex 处，基准元素放置到最终正确位置上
        swap(arr, right, storeIndex);
        return storeIndex;
    }

    function sort(arr, left, right) {
        if (left > right) return;
        var storeIndex = partition(arr, left, right);
        sort(arr, left, storeIndex - 1);
        sort(arr, storeIndex + 1, right);
    }

    sort(arr, 0, arr.length - 1);
    return arr;
}
```


## 堆排序 (Heap Sort)

堆排序是指利用堆这种数据结构所设计的一种排序算法。

### 算法思路及演示

在给出堆排序算法之前，首先介绍几个基本概念：

1. 二叉树

    在计算机科学中，二叉树是每个节点至多有两个子树的树结构，常被用于实现二叉查找树和二叉堆。二叉树子树被称作“左子树”(left subtree)和“右子树”(right subtree)，次序不可颠倒。

    二叉树分为完全二叉树(complete binary tree)和满二叉树(full binary tree)。
    
    - 完全二叉树：深度为 `k`，有 `n` 个节点的二叉树，当且仅当其每一个节点都与深度为 `k` 的满二叉树中序号为 `1` 至 `n` 的节点对应时，称之为完全二叉树。
    
        深度为 `3` 的完全二叉树：
        ![complete_binary_tree.png](https://i.loli.net/2018/06/02/5b128ca63b62f.png)

    - 满二叉树：一棵深度为 `k`，且有 `2k - 1` 个节点称之为满二叉树。
    
        深度为 `3` 的满二叉树：
        ![full_binary_tree.png](https://i.loli.net/2018/06/02/5b128c9010197.png)

2. 堆

    堆（二叉堆）可视为一棵完全二叉树，完全二叉树除最底层之外，每一层都是满的，这使得堆可以利用数组来表示（普通的一般的二叉树通常用链表作为基本容器表示），每一个结点对应数组中的一个元素。

    下图是一个堆和数组的相互关系：
    ![heap-and-array.png](https://i.loli.net/2018/06/02/5b128c17be234.png)

    对于给定的某结点的下标 `i`，容易计算出该结点的父结点、子结点的下标：
    
    ```
    Parent(i) = floor(i / 2)   // i 的父节点下标
    Left(i) = 2i   // i 的左子节点下标
    Right(i) = 2i + 1   // i 的右子节点下标
    ```

    二叉堆一般分为两种：最大堆和最小堆。
    
    最大堆：
    
    - 最大堆中的最大元素值出现在根结点（堆顶）
    - 堆中每个父节点的元素值都大于等于其孩子结点（如果存在）

    ![max-heap.png](https://i.loli.net/2018/06/02/5b128cc148a54.png)

    最小堆：
    - 最小堆中的最小元素值出现在根结点（堆顶）
    - 堆中每个父节点的元素值都小于等于其孩子结点（如果存在）

    ![min-heap.png](https://i.loli.net/2018/06/02/5b128af9118ec.png)

<br/>

3. 堆排序原理

    堆排序就是把最大堆堆顶的最大数取出，将剩余的堆调整为最大堆，再次将堆顶的最大数取出，此过程持续至剩余数只有一个时结束。操作中有如下定义：

    - 最大堆调整 (Max-Heapify)：将堆的末端子节点作调整，使得子节点永远小于父节点
    - 创建最大堆 (Build-Max-Heap)：将堆所有数据重新排序，使其成为最大堆
    - 堆排序 (Heap-Sort)：移除位在第一个数据的根节点，并做最大堆调整的递归运算

    继续进行下面的讨论前，需要注意的一个问题是，数组都是 `Zero-Based`，这就意味着堆数据结构模型要发生改变:

    ![heap-and-array-zero-based.png](https://i.loli.net/2018/06/02/5b128c4640519.png)

    相应的计算公式也要作出相应调整：

    ```
    Parent(i) = floor((i - 1) / 2)     // i 的父节点下标
    Left(i) = 2i + 1     // i 的左子节点下标
    Right(i) = 2(i + 1)     // i 的右子节点下标
    ```

4. 最大堆调整 (MAX‐HEAPIFY)

    保持最大堆性质，是创建最大堆的核心子程序，作用过程如图：
    
    ![MAX_HEAPIFY-_Procedure.png](https://i.loli.net/2018/06/02/5b128cfdc7641.png)

    由于一次调整后，堆仍然违反堆性质，这里用迭代的方法使得整个堆都满足堆性质： 
    ```javascript
    /**
     - 从 index 开始检查并保持最大堆性质
     *
     - @arr
     *
     - @index 检查的起始下标
     *
     - @heapSize 堆大小
     *
     **/
    function maxHeapify(arr, index, heapSize) {
      var iMax, iLeft, iRight;
      while (true) {
        iMax = index;
        iLeft = 2 * index + 1;
        iRight = 2 * (index + 1);
        if (iLeft < heapSize && arr[index] < arr[iLeft]) {
          iMax = iLeft;
        }
        if (iRight < heapSize && arr[iMax] < arr[iRight]) {
          iMax = iRight;
        }
        if (iMax != index) {
          swap(arr, iMax, index);
          index = iMax;
        } else {
          break;
        }
      }
    }
    function swap(arr, i, j) {
      var temp = arr[i];
      arr[i] = arr[j];
      arr[j] = temp;
    }
    ```

5. 创建最大堆 (Build-Max-Heap)

    作用是将一个数组改造成一个最大堆，接受数组和堆大小两个参数，`Build-Max-Heap` 将自下而上的调用 `Max-Heapify` 来改造数组，建立最大堆。因为 `Max-Heapify` 能够保证下标为 `i` 的结点之后的结点都满足最大堆的性质，所以自下而上的调用 `Max-Heapify` 能够在改造过程中保持这一性质。如果最大堆的数量元素是 `n`，那么 `Build-Max-Heap` 从 `Parent(n)` 开始，往上依次调用 `Max-Heapify`。流程如下：

    ![building-a-heap.png](https://i.loli.net/2018/06/02/5b128d2496ceb.png)
    
    相关 JavaScript 代码：

    ```javascript
    function buildMaxHeap(array, heapSize) {
      var i,
          iParent = Math.floor((heapSize - 1) / 2);
          
      for (i = iParent; i >= 0; i--) {
        maxHeapify(array, i, heapSize);
      }
    }
    ```

6. 堆排序 (Heap-Sort)
    
    为堆排序的接口算法，`Heap-Sort` 先调用 `Build-Max-Heap` 将数组改造为最大堆，然后将堆顶和堆底元素交换，之后将底部上升，最后重新调用 `Max-Heapify` 保持最大堆性质。由于堆顶元素必然是堆中最大的元素，所以一次操作之后，堆中存在的最大元素被分离出堆，重复 `n - 1`次之后，数组排列完毕。整个流程如下：

    ![Heap_Sort.png](https://i.loli.net/2018/06/02/5b128c62450e6.png)

    相关 JavaScript 代码：
    ```javascript
    function heapSort(array, heapSize) {
      buildMaxHeap(array, heapSize);
      for (int i = heapSize - 1; i > 0; i--) {
        swap(array, 0, i);
        maxHeapify(array, 0, i);
      }  
    }
    ```

### JS实现

```javascript
function heapSort(arr) {
  function swap(arr, i, j) {
    var temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
  }
  function maxHeapify(arr, index, heapSize) {
    var iMax,
      iLeft,
      iRight;
    while (true) {
      iMax = index;
      iLeft = 2 * index + 1;
      iRight = 2 * (index + 1);
      if (iLeft < heapSize && arr[index] < arr[iLeft]) {
        iMax = iLeft;
      }
      if (iRight < heapSize && arr[iMax] < arr[iRight]) {
        iMax = iRight;
      }
      if (iMax != index) {
        swap(arr, iMax, index);
        index = iMax;
      } else {
        break;
      }
    }
  }
  function buildMaxHeap(arr) {
    var i,
      iParent = Math.floor(arr.length / 2) - 1;
    for (i = iParent; i >= 0; i--) {
      maxHeapify(arr, i, arr.length);
    }
  }
  function sort(arr) {
    buildMaxHeap(arr);
    for (var i = arr.length - 1; i > 0; i--) {
      swap(arr, 0, i);
      maxHeapify(arr, 0, i);
    }
    return arr;
  }
  return sort(arr);
}
```


## 计数排序 (Counting sort)

计数排序的核心在于将输入的数据值转化为键存储在额外的数组空间中。

计数排序使用一个额外的数组 `C`，其中第 `i` 个元素是待排序数组A中值等于 `i` 的元素的个数，然后根据数组 `C` 来将 `A` 中的元素排到正确的位置。

计数排序是型非比较排序，其速度快于任何比较排序算法。

作为一种线性时间复杂度的排序，它只能对有确定范围的整数进行排序。当输入的元素是 `n` 个 `0` 到 `k` 之间的整数时，它的运行时间是 `O(n + k)`。

由于用于计数的数组 `C` 的长度取决于待排序数组中数据的范围（等于待排序数组的最大值与最小值的差加上 `1`），这使得计数排序处理数据范围很大的数组需要大量时间和内存。例如：计数排序是用来排序0到100之间的数字的最好的算法，但是它不适合按字母顺序排序人名。但是，计数排序可以用在基数排序中的算法来排序数据范围很大的数组。

### 算法思路
1. 找出待排序数组中最大及最小元素。
2. 统计数组中每个值为 `i` 的元素出现的次数，存入数组 `C` 的第 `i` 项。
3. 对所有的计数累加（从 `C` 中的第一个元素开始，每一项和前一项相加）。
4. 反向填充目标数组：将每个元素 `i` 放在新数组的第 `C(i)` 项，每放一个元素就将 `C(i)` 减去 `1`。


### 实例演示

![Counting_sort.gif](https://i.loli.net/2018/06/02/5b1288b82cb7d.gif)

### JS实现

```javascript
function countingSort(iArr, max) {
    var n = iArr.length;
    var oArr = [];
    // 创建长度max的数组，填充0
    var C = [];
    for(var i = 0; i <= max; i++){
        C[i] = 0;
    }
    // 遍历输入数组，填充C
    for(var j = 0; j < n; j++){
        C[iArr[j]]++;
    }
    // 遍历C，输出数组
    for(var k = 0; k <= max; k++){
        // 按顺序将值推入输出数组，并将对应标志位减1
        while(C[k]-- > 0){
            oArr.push(k);
        }
    }
    return oArr;
}
```


## 桶排序 (Bucket Sort)

桶排序原理是将数组分入有限数量的桶里，然后再对每个桶分别排序（可能以递归方式继续使用桶排序或其它算法），最后将各个桶中的数据有序地合并起来。

桶排序在大多数情况下是常见排序里最快的一种，比快排还要快，缺点是极耗空间，基本上是最耗空间的一种排序算法，而且只能在某些情形下使用。

为使桶排序更加高效，我们需要做到这两点：

1. 在额外空间充足的情况下，尽量增大桶的数量。
2. 使用的映射函数能够将输入的 `N` 个数据均匀分配到 `K` 个桶中，同时对于桶中元素的排序，比较排序算法的选择对于性能的影响至关重要。

Best Cases：当输入的数据可均匀分配到每一个桶中

Worst Cases：当输入的数据被分配至同一个桶中

[Data Structure Visualizations](http://www.cs.usfca.edu/~galles/visualization/BucketSort.html)   提供了一个桶排序的分步动画演示。

### 算法思路
1. 假设待排序的一组数统一地分布在一个范围，并将这一范围划分为几个子范围，也就是桶。
2. 将待排序的一组数，分档归入这些子桶，并对桶中数据进行排序。
3. 将各个桶中的数据有序合并。

### 实例演示

设有数组`[23, 28, 3, 46, 12, 35, 17, 40]`，则数组最大值为 `46`，设置 `5` 个桶，每个桶可存放数的范围为：`0~9`、`10~19`、`20~29`、`30~39`、`40~49`，分别将数据放入各自所属的桶，然后对每个桶里的数排序，或在将数放入桶的同时用插入算法进行排序。最后，将各个桶中的数据有序地合并起来。
```
 3    12,17   23,28    35     46,40
 |      |       |       |       |     →
0~9   10~19   20~29   30~39   40~49

[3, 12, 17, 23, 28, 35, 40, 46]
```

### JS实现

```javascript
/*
* @arr 将要排序的数组
*
* @step 划分桶的步长，比如 step = 5，表示每个桶存放的数字的范围是 5，像 -4<sub>1、0</sub>5、6~11
*/
function bucketSort(arr, step) {
    var result = [],
        bucket = [],
        bucketCount,
        l = arr.length,
        i,
        j,
        k,
        s,
        max = arr[0],
        min = arr[0],
        temp;
    for (i = 1; i < l; i++) {
        if (arr[i] > max) {
            max = arr[i]
        }
        if (arr[i] < min) {
            min = arr[i];
        }
    }
    min = min - 1;
    bucketCount = Math.ceil((max - min) / step); // 需要桶的数量
    for (i = 0; i < l; i++) {
        temp = arr[i];
        for (j = 0; j < bucketCount; j++) {
            if (temp > (min + step * j) && temp <= (min + step * (j + 1))) { // 判断放入哪个桶
                if (!bucket[j]) {
                    bucket[j] = [];
                }
                // 通过插入排序将数字插入到桶中的合适位置
                s = bucket[j].length;
                if (s > 0) {
                    for (k = s - 1; k >= 0; k--) {
                        if (bucket[j][k] > temp) {
                            bucket[j][k + 1] = bucket[j][k];
                        } else {
                            break;
                        }
                    }
                    bucket[j][k + 1] = temp;
                } else {
                    bucket[j].push(temp);
                }
            }
        }
    }
    for (i = 0; i < bucketCount; i++) { // 循环取出桶中数据
        if (bucket[i]) {
            k = bucket[i].length;
            for (j = 0; j < k; j++) {
                result.push(bucket[i][j]);
            }
        }
    }
    return result;
}
```


## 基数排序 (Radix Sort)

基数排序是一种非比较型的整数排序算法，其原理是将整数按位数切割后依次比较。
该排序会使用到桶(Bucket)，顾名思义，通过将要比较的位，把要排序的元素分配至 `0~9` 个桶中，以达到排序的作用。
在某些时候，基数排序法的效率高于其它比较型排序法。

### 算法思路

1. 将所有待比较数值（正整数）统一为同样的数位长度，数位较短的数前面补零。
2. 从最低位开始，依次进行排序至最高位排序完成。

### 实例演示

基数排序的方式可采用 `LSD (Least sgnificant digital)` 或 `MSD (Most sgnificant digital)`，`LSD` 的排序方式从键值的最右侧开始，而 `MSD` 则相反，从键值左侧开始。

以 `LSD` 为例，考虑数组 `[36, 9, 10, 25, 1, 49, 63, 16, 81, 32, 24, 7, 18, 82]`：

![Radix_Sort.gif](https://i.loli.net/2018/06/02/5b12895329f10.gif)

首先根据个位的值 `=` 桶编号的方式，将所有元素依次分配至编号 `0` 到 `9` 的桶中：

编号 |  0  |  1 |  2  |   3   |   4   |    5   |  6  |   7  |  8  |  9  
:--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--:|:--:
1  | 10 | 1 | 32 | 63 | 24 | 25 | 36 | 7 | 18 | 9
2  |    | 81 | 82 |   |    |    | 16 |   |    | 49
  
然后，将这些数字按照桶以及桶内部的排序连接起来：
[10, 1, 81, 32, 82, 63, 24, 25, 36, 16, 7, 18, 9, 49]

接着按照十位的数值，分别对号入座：

编号 |  0  |  1 |  2  |   3   |   4   |    5   |  6  |   7  |  8  |  9  
:--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--:|:--:
1  | 1 | 10 | 24 | 32 | 49 |    | 63 |   | 81 | 
2  | 7 | 16 | 25 | 36 |    |    |    |   | 82 | 
3  | 9 | 18 |    |    |    |    |    |   |    |

最后按照次序重现连接，完成排序：
`[1, 7, 9, 10, 16, 18, 24, 25, 32, 36, 49, 63, 81, 82]`

### JS实现

```javascript
function radixSort(arr) {
    var bucket = [],
        len = arr.length,
        loop,
        str,
        i,
        j,
        k,
        t,
        max = arr[0];
    for (i = 1; i < l; i++) {
        if (arr[i] > max) {
            max = arr[i]
        }
    }
    loop = (max + '').length;
    for (i = 0; i < 10; i++) {
        bucket[i] = [];
    }
    for (i = 0; i < loop; i++) {
        for (j = 0; j < l; j++) {
            str = arr[j] + '';
            if (str.length >= i + 1) {
                k = parseInt(str[str.length - i - 1]);
                bucket[k].push(arr[j]);
            } else { // 高位为 0
                bucket[0].push(arr[j]);
            }
        }
        arr.splice(0, l);
        for (j = 0; j < 10; j++) {
            t = bucket[j].length;
            for (k = 0; k < t; k++) {
                arr.push(bucket[j][k]);
            }
            bucket[j] = [];
        }
    }
    return arr;
}
```


## 各排序算法特点小结

算法 | 平均时间复杂度 | 最优时间复杂度 | 最差时间复杂度 | 空间复杂度 | 稳定性 
:----: | :----: | :----: | :----: | :----: | :----: 
冒泡排序    |  O(n²)  | O(n) | O(n²) | O(1) | 稳定 
鸡尾酒排序  |  O(n²)  | O(n²) | O(n²) | O(1) | 稳定     
选择排序    |  O(n²)  | O(n²) | O(n²) | O(1) | 不稳定  
插入排序    |  O(n²)  | O(n) | O(n²) | O(1) | 稳定   
希尔排序    | O(nlogn) | O(nlog²n) | O(n²) | O(1) | 不稳定  
归并排序    | O(nlogn) | O(n) | O(nlogn) | O(n) | 稳定
快速排序    | O(nlogn) | O(nlogn) | O(n²) | 最优O(n) 最差O(logn) | 不稳定
堆排序      | O(nlogn) | O(nlogn) | O(nlogn) | O(1) | 不稳定  
计数排序    |  O(n+k)  | O(n+k) | O(n+k) | O(n+k) | 稳定
桶排序      |  O(n+k)  | O(n+k) | O(n²) | O(n*k) | 稳定
基数排序    |  O(n*k)  | O(n*k)| O(n*k) | O(n+k) | 稳定

- *n: 数据规模*
- *k: 桶的个数*


## Bonus：Javascript 随机数组

在学习排序算法的时候，经常要用到随机数组，于是就写了一个生成随机数组的方法。

原理是先从所有元素中随机选取一个与第一个元素进行交换，然后从第二个开始选择一个元素与第二个交换，直到最后一个元素。这样能确保每个元素在每个位置的概率都是 `1/n`。

代码如下：
```javascript
/**
 *
 * 生成从 1 到 length 之间的随机数组
 *
 * @length 随机数组的长度，如果未传递该参数，那么 length 为默认值 9
 *
 */
function randomarr(length) {
    var i,
        index,
        temp,
        arr = [length];
    length = typeof(length) === 'undefined' ? 9 : length;
    for (i = 1; i <= length; i++) {
        arr[i - 1] = i;
    }
    // 打乱数组
    for (i = 1; i <= length; i++) {
        // 产生从 i 到 length 之间的随机数
        index = parseInt(Math.random() * (length - i)) + i;
        if (index != i) {
            temp = arr[i];
            arr[i] = arr[index];
            arr[index] = temp;
        }
    }
    return arr;
}
```

<br/>

参考资料：
- [排序算法-维基百科](https://zh.wikipedia.org/wiki/%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95)
- [JS家的排序算法](http://www.jianshu.com/p/1b4068ccd505)
- [排序算法可视化教程](https://visualgo.net/en/sorting)
