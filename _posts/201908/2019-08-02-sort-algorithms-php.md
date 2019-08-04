---
layout: post
title:  "五种常见的排序算法及其PHP实现"
categories: tech
img: 2019pics/sorting-algorithms.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [bubble sort, insertion sort, selection sort, merge sort, quick sort, algorithm, time complexity, space complexity]
excerpt: 勿在浮沙筑高台。算法是程序员知识体系中很重要的组成部分，本文介绍了几个常见的排序算法，以及它们的php代码实现。
---

## 排序算法的分析方法

学习排序算法，我们除了学习它的算法原理、代码实现之外，更重要的是要学会如何评价、分析一个排序算法。分析一个排序算法，需要从以下几个方面入手。

### 排序算法的执行效率

对于排序算法执行效率的分析，一般需要从这几个方面来衡量：

#### 最好情况、最坏情况、平均情况时间复杂度

我们在分析排序算法的时间复杂度时，要分别给出最好、最坏、平均情况下的时间复杂度。除此之外，还要说出他们分别对应的要排序的原始数据是什么样的。

#### 时间复杂度的系数、常数、低阶

我们知道，时间复杂度反映的是数据规模n很大的时候的一个增长趋势，所以它标识的时候回忽略系数、常数、低阶。但是实际的软件开发中，我们排序的可能是10个、100个、1000个这样规模很小的数据，所以，在对同一阶时间复杂度的排序算法性能对比的时候，我们就要把系数、常数、低阶也考虑进来。

#### 比较次数和交换（或移动）次数

本文的几种算法都是基于比较的，而基于比较的排序算法的执行过程，会涉及两种操作，一种是元素比较大小，另一种是元素交换或移动。所以，分析排序算法的实行效率的时候，应该把比较次数和交换（或移动）次数也考虑进去。

### 排序算法的内存消耗

排序算法的内存消耗可以通过空间复杂度来衡量，特殊地，空间复杂度为O（1）的排序算法被称为**原地排序**（sorted in place）。

### 排序算法的稳定性

仅仅用执行效率和内存消耗来衡量排序算法的好坏是不够的。针对排序算法，我们还有一个重要的度量指标，**稳定性**。这个概念是说，如果待排序的序列中存在值相等的元素，经过排序之后，相等元素之间原有的先后顺序不变。

## 冒泡排序(Bubble Sort)

### 原理

冒泡排序只会操作相邻的两个数据，每次冒泡操作都会对相邻的两个元素进行比较，看是否满足大小关系要求。如果不满足就让它俩互换。一次冒泡会让至少一个元素移动到它应该在的位置。重复n次，就完成了n个数据的排序工作。

举个栗子。对于一组数据4,5,6,3,2,1，从小到大进行排序，第一次冒泡排序的详细过程就如下图。

![bubble-sort]({{site.baseurl}}/assets/img/2019pics/sorting-algorithms-bubble-sort.jpg)

可以看出，经过一次冒泡操作后，6已经在正确的位置上了。要想完成所有数据的排序，我们只需要进行6次这样的冒泡操作。

### 代码

bubble_sort.php
```
//冒泡排序将一个数组从小到大排列
function bubble_sort($arr)
{
    if (empty($arr)) return [];

    $length = count($arr);

    foreach ($arr as $i => $v) {
        $flag = false;//用来标记是否有交换，若说明排序完成
        for ($j = 0; $j < $length - $i - 1; $j++) {
        	//如果当前位置的值比后面的大，则交换顺序
            if ($arr[$j] > $arr[$j + 1]) {
                $tmp         = $arr[$j];
                $arr[$j]     = $arr[$j + 1];
                $arr[$j + 1] = $tmp;
                $flag        = true;
            }
        }

        if (!$flag) break;
    }

    return $arr;
}
```

### 分析

* 由于冒泡的过程只涉及相邻数据的交换操作，因此冒泡排序是一个原地排序算法。
* 只有交换才可以改变两个元素的前后顺序。为了保证冒泡排序算法的稳定性，当有相邻两个元素大小相等的时候，我们不做交换，相同大小的数据在排序前后不会改变顺序，所以冒泡排序是稳定的排序算法。
* 最好情况下，要排序的数据已经是有序的了，我们只需要进行一次冒泡操作，就可以结束了，所以最好情况时间复杂度为O(n)。而最坏情况是，要排序的数据刚好是倒序排列的，我们要进行n次冒泡操作，所以最坏情况时间复杂度是O(n^2)。

## 插入排序（Insertion Sort）

### 原理

将数组中的数据分为两个区间，已排序区间和未排序区间。初始已排序区间中只有一个元素，就是数组的第一个元素。插入算法的核心思想是取未排序区间中的元素，在已排序区间中找到合适的插入位置将其插入，并保证已排序区间数据一直有序。重复这个过程，直到未排序区间中元素为空，算法结束。

举个栗子，要排序的数据是4,5,6,1,3,2，其中左侧为已排序区间，右侧是未排序区间。

![insertion-sort]({{site.baseurl}}/assets/img/2019pics/sorting-algorithms-insertion-sort.jpg)

插入排序也包含两种操作，一种是元素的比较，一种是元素的移动。当我们需要将一个数据a插入到已排序区间时，需要拿a与已排序区间的元素依次比较大小，找到合适的插入位置。找到插入点之后，我们还需要将插入点之后的元素顺序往后移动一位，这样才能腾出位置给元素a插入。

### 代码
insertion_sort.php
```
function insertion_sort($arr)
{
    if (empty($arr)) return [];

    $length = count($arr);
    for ($i=1; $i<= $length -1;$i ++) {
        $value = $arr[$i];//取出数据a
        //遍历已排序区间，如果比a大，则往后移动一位，给a腾位置出来
        for ($j=$i-1; $j>=0; --$j) {
            if ($arr[$j] > $value) {
                $arr[$j+1] = $arr[$j];
            } else {
                break;
            }
        }
        $arr[$j+1] = $value;//将a插入数组
    }

    return $arr;
}
```

### 分析

* 从实现过程可以很明显的看出，插入排序算法的运行并不需要额外的存储空间，所以空间复杂度是O（1），也就是一个原地排序算法。
* 对于值相同的元素，我们可以选择将后面出现的元素，插入到前面出现元素的后面，这样就可以保持原有的前后顺序不变，所以插入排序是稳定的排序算法。
* 如果数组是有序的，我们就不需要搬移任何数据。如果我们从尾到头在有序数据组里面查找插入位置，每次只需要比较一个数据就能确定插入的位置。所以这种情况下，最好时间复杂度为O（n）。注意是**从尾到头便利已经有序的数据**。如果数组是倒序的，每次插入都相当于在数组的第一个位置插入新的数据，需要移动大量的数据，所以最坏情况时间复杂度为O（n^2）。对于插入排序来说，每次插入操作都相当于在数组中插入一个数据，循环执行n次插入操作，所以平均时间复杂度为O（n^2）。

## 选择排序

### 原理

选择排序算法的实现思路有点类似插入排序，也分已排序区间和未排序区间。但选择排序每次会从未排区间中找到最小的元素，将其放到已排序区间的末尾。

举个栗子，要排序的数据是4,5,6,1,3,2，其中左侧为已排序区间，右侧是未排序区间。

![selection-sort]({{site.baseurl}}/assets/img/2019pics/sorting-algorithms-selection-sort.jpg)

选择排序中，每次排序都是为了找到当前未排序区间中最小的那个数据，并将它放到已排序区间的末尾。

### 代码
selection_sort.php
```
function selection_sort($arr)
{
    if (empty($arr)) return [];

    $length = count($arr);
    for ($i = 0; $i <= $length - 1; $i++) {
        $anchor = $i;//未排序区间的头部，已排序区间的末尾
        $value  = $arr[$i];//记录未排序区间的值，准备与找到的最小值交换
        $min    = $value;//最小值初始化
        $flag   = false;//如果找不到更小值，说明已经有序，停止即可
        for ($j = $i + 1; $j <= $length - 1; $j++) {
        //遍历未排序区间，找到其最小值，记录下它的位置
            if ($arr[$j] < $min) {
                $min    = $arr[$j];
                $anchor = $j;
                $flag   = true;
            }
        }
        if (!$flag) {
            break;
        }
        //如果有最小值交换，将其放到已排序区间的末尾
        $arr[$i]      = $min;
        $arr[$anchor] = $value;
    }


    return $arr;
}
```

### 分析

* 选择排序空间复杂度为O(1)，是一种原地排序算法。
* 由于选择排序每次都要找剩余未排序元素中的最小值，并和前面的元素交换位置，这样破坏了稳定性。比如5,8,5,2,9，这样一组数据，使用选择排序算法的时候，第一次找到最小元素2，与第一个5交换位置，那第一个5和中间的5顺序就变了，所以就不稳定了。因此相对于冒泡和插入，选择排序会略微逊色。
* 由于每次都要遍历未排序区间，因此选择排序的最好、最坏、平均时间复杂度都是O（n^2）。

## 归并排序

### 原理

如果要排序一个数组，我们先把数组从中间分成前后两部分，然后对前后两部分分别排序，再将排序号的两部分合并在一起，这样整个数组就都有序了。
![merge-sort]({{site.baseurl}}/assets/img/2019pics/sorting-algorithms-merge-sort.jpg)

### 代码
merge_sort.php
```
function merge_sort($arr)
{
    $length = count($arr);
    if ($length <= 1) return $arr;//递归退出条件

    $midIndex = intval($length / 2);

    $left  = array_slice($arr, 0, $midIndex);
    $right = array_slice($arr, $midIndex);


    $left  = merge_sort($left);
    $right = merge_sort($right);

    return merge($left, $right);
}

function merge($arr1, $arr2)
{
    $resultArr = [];
    foreach ($arr1 as $key1 => $v1) {
        foreach ($arr2 as $key2 => $v2) {
            if ($v1 < $v2) {
                $resultArr[] = $v1;
                unset($arr1[$key1]);//放入resultArr中的元素要删除
                break;//以$arr1为基准，因为$arr1 已经有序（从小到大），因此如果$v1 < $v2，说明要继续看$arr1的下一个元素是否比$v2大。终止当前内部的循环即可
            } else {
            //如果比$v2大，则继续看$arr2中的元素是否比$v1大
                $resultArr[] = $v2;
                unset($arr2[$key2]);//放入resultArr中的元素要删除
            }
        }
    }

    foreach ($arr1 as $v) {
        $resultArr[] = $v;
    }

    foreach ($arr2 as $v) {
        $resultArr[] = $v;
    }

    return $resultArr;
}
```

### 分析

* 归并排序的稳定性主要看merge函数，在代码中我们是先把左边的数组元素放入结果数组，这样可以保证值相同的元素，在合并前后的先后顺序不变。因此归并排序是一个稳定的排序算法。
* 时间复杂度需要通过复杂的推导，这里不做论述。归并排序的最好、最坏、平均时间复杂度都是O(nlgn)。
* 尽管归并排序的合并操作每次都需要申请额外的内存空间，但在合并完成之后，临时开辟的内存空间就被释放掉了。在任意时刻，CPU只会有一个函数在执行。临时内存空间最大也不会超过n个数据的大小，因此空间复杂度是O（n）。

## 快速排序

### 原理

如果要排序一个数组，选择任意一个元素作为基准点，小于该值的放到基准点的左边，大于等于该值的放到基准点的右边，如此循环往复，直到数组的元素个数小于等于1时退出，这样整个数组就都有序了。
![quick-sort]({{site.baseurl}}/assets/img/2019pics/sorting-algorithms-quick-sort.jpg)

### 代码1
```
function quick_sort($arr)
{
    $length = count($arr);
    if ($length <= 1) return $arr;

    $middle = $arr[0];
    $left   = [];
    $right  = [];

    for ($i = 1; $i < $length; $i++) {
        if ($middle > $arr[$i]) {
            $left[] = $arr[$i];
        } else {
            $right[] = $arr[$i];
        }
    }

    $left  = quick_sort($left);
    $right = quick_sort($right);

    return array_merge($left, [$middle], $right);
}
```
> 网上的大部分版本是代码1，在这里每次交换，都会开辟新的空间，这种情况下的快排，不是原地排序算法。但是在交换的时候，只有小于`$middle`的数才会被放到左边去，因此这种情况下快排是稳定的排序算法。改进版本可以将`$middle`赋值给`0~count($arr) -1`之间的任何一个整数增加随机性，可以证明在此时快排的时间复杂度为O(nlgn)。

### 代码2
```
function quick_sort_init($arr)
{
    quick_sort($arr, 0, count($arr) - 1);

    return $arr;
}


function quick_sort(&$arr, $p, $r)
{
    if ($p >= $r) {
        return;
    }

    $i = partition($arr, $p, $r);

    quick_sort($arr, $p, $i - 1);
    quick_sort($arr, $i + 1, $r);

    return;
}


function partition(&$arr, $p, $r)
{
    $pivot = $arr[$r];
    $i     = $p;

    for ($j = $p; $j <= $r - 1; $j++) {
        if ($arr[$j] < $pivot) {
            $tmp     = $arr[$j];
            $arr[$j] = $arr[$i];
            $arr[$i] = $tmp;
            $i++;
        }
    }
    $arr[$r] = $arr[$i];
    $arr[$i] = $pivot;

    return $i;
}
```

> 代码2 跟代码1 的主要区别在于，代码2 中通过partition函数实现了一个原地排序算法，空间复杂度大大降低了。`partition`部分的伪代码如下

```
partition(A, p, r) {
  pivot := A[r]
  i := p
  for j := p to r-1 do {
    if A[j] < pivot {
      swap A[i] with A[j]
      i := i+1
    }
  }
  swap A[i] with A[r]
  return i
```
它的含义是通过游标`i(pivot = A[i])`把`A[p, r-1]`分成两部分。`A[p, i-1]`的元素都是小于pivot的，我们暂且称为“已处理区间”；`A[i, r-1]`是“未处理区间”。我们每次都从未处理区间的`A[i, r-1]`中取一个数，将其与pivot对比，如果小于pivot，则将其加入已处理区间的尾部，即A[i]的位置，这里采用交换的方式将`A[j]`交换给了`A[i]`。下图是处理过程
![quick-sort-partition]({{site.baseurl}}/assets/img/2019pics/sorting-algorithms-quick-sort-partition.jpg)

> 代码2中，因为分区操作涉及交换，如果数组中有两个相同的元素，比如序列8,7,6,8,4，在经历过第一次分区操作后，两个8的相对先后顺序就会该表，所以此时的快排不是一个稳定的排序算法。

### 分析

* 可以证明，快速排序在大部分情况下的时间复杂度是O(nlgn)，在极端情况下（数组已经有序时）为O(n^2)。
> 快排是一个很经典的排序方法，本文介绍的也只是冰山一角，据了解还有很多改进的算法，比如如何将时间复杂度大概率下保证为O(nlgn)。等了解了之后再来更新，这篇文章就先到这里。






