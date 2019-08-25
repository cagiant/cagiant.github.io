---
layout: post
title:  "二分查找及其相关常见变形的PHP实现"
categories: tech
img: 2019pics/binary-search.jpg # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [binary search]
excerpt: 
---

> 二分查找针对的是一个有序的数据集合，查找思想有点类似分治思想。每次都通过跟区间的中间元素对比，将待查找的区间缩小为之前的一半，直到找到要查找的元素，或者区间被缩小为0。

下面介绍了几种情况下二分算法的PHP实现，对于有序数组，默认是从小到大排列。

## 情况1. 有序数组中不存在重复元素，我们在其中用二分查找值等于给定值的数据

这是最简单的二分查找，代码如下：
```
function bsearch($arr, $value)
{
    $n = count($arr);

    $low  = 0;
    $high = $n - 1;

    while ($low <= $high) {//循环退出条件
        $mid = intval(($low + $high) / 2);
        if ($arr[$mid] == $value) {
            return $mid;
        } elseif ($arr[$mid] > $value) {
            $high = $mid - 1;//发现value对应的元素在mid的左边，将右边的区间缩小
        } else {
            $low = $mid + 1;//发现value对应的元素在mid的右边，将左边的区间缩小
        }
    }

    return -1;//找不到时返回-1
}
```

## 情况2. 有序数组中存在重复元素，查找第一个值等于给定值的元素

```
function bsearch($arr, $value)
{
    $n = count($arr);

    $low  = 0;
    $high = $n - 1;

    while ($low <= $high) {
        $mid      = intval(($low + $high) / 2);
        $midValue = $arr[$mid];
        if ($midValue > $value) {
            $high = $mid - 1;
        } elseif ($midValue < $value) {
            $low = $mid + 1;
        } else {
            if ($mid == 0 || $arr[$mid - 1] != $value) {//如果当前值是第一个，或者当前值前面的值不等于value，说明mid对应的元素已经是第一个满足条件的元素。
                return $mid;
            } else {
                $high = $mid - 1;//否则即mid前面的一个元素还满足条件，说明区间还需要继续往左缩小
            }
        }
    }

    return -1;
}
```

## 情况3. 有序数组中存在重复元素，查找最后一个值等于给定值的元素

```
function bsearch($arr, $value)
{
    $n = count($arr);

    $low  = 0;
    $high = $n - 1;

    while ($low <= $high) {
        $mid      = intval(($low + $high) / 2);
        $midValue = $arr[$mid];
        if ($midValue > $value) {
            $high = $mid - 1;
        } elseif ($midValue < $value) {
            $low = $mid + 1;
        } else {
            if ($mid == $n - 1 || $arr[$mid + 1] != $value) {//如果当前值已经是最后一个，或者当前值后面的元素已经不等于value，说明当前值满足条件。
                return $mid;
            } else {
                $low = $mid + 1;//否则说明mid后面的一个元素满足条件，区间继续往右缩小
            }
        }
    }

    return -1;
}
```

## 情况4. 有序数组存在重复元素，查找第一个大于等于给定值的元素

```
function bsearch($arr, $value)
{
    $n = count($arr);

    $low  = 0;
    $high = $n - 1;

    while ($low <= $high) {
        $mid      = intval(($low + $high) / 2);
        $midValue = $arr[$mid];
        if ($midValue < $value) {
            $low = $mid + 1;
        } else {
            if ($mid == 0 || $arr[$mid - 1] < $value) {//如果当前值已经是第一个，或者mid前面的一个元素已经小于value，说明当前元素满足条件
                return $mid;
            } else {
                $high = $mid - 1;//否则说明mid前面的一个元素仍大于等于value，往左继续缩小区间。
            }
        }
    }

    return -1;
}
```

## 情况5. 有序数组存在重复元素，查找最后一个小于等于给定值的元素
```
function bsearch($arr, $value)
{
    $n = count($arr);

    $low  = 0;
    $high = $n - 1;

    while ($low <= $high) {
        $mid      = intval(($low + $high) / 2);
        $midValue = $arr[$mid];
        if ($midValue > $value) {
            $high = $mid - 1;
        } else {
            if ($mid == $n - 1 || $arr[$mid + 1] > $value) { //如果当前元素已经是最后一个，或者它的后面的一个元素已经大于value，说明满足条件，返回即可。
                return $mid;
            } else {
                $low = $mid + 1;//否则说明满足条件的元素在mid后面，继续缩小区间
            }
        }
    }

    return -1;
}
```

## 情况6. 循环有序数组（e.g.[3,4,5,6,1,2]），查找等于给定值的元素
```
function bsearch($arr, $value)
{
    $left  = 0;
    $right = count($arr) - 1;

    while ($left <= $right) {
        $mid        = intval(($left + $right) / 2);
        $midValue   = $arr[$mid];
        $leftValue  = $arr[$left];
        $rightValue = $arr[$right];

        if ($midValue == $value) {
            return $mid;
            //将数组一分为2，总有一边是完全有序的，识别出哪边有序，然后再缩小查找区间
        } elseif ($midValue > $rightValue) {
            if ($midValue > $value && $value >= $leftValue) {
            	//左边数组有序，如果目标数在左边区间内，往左缩小区间
                $right = $mid - 1;
            } else {
                $left = $mid + 1;//否则往右缩小区间
            }
        } else {
            if ($midValue < $value && $value <= $rightValue) {
            	//右边数组有序，如果目标数在右边区间，往右缩小区间
                $left = $mid + 1;
            } else {
                $right = $mid - 1;//否则往左缩小区间
            }
        }
    }

    return -1;
}
```

## 总结

* 二分查找的时间复杂度很小，是指数级递减的O(lgn)。
* 二分查找应用场景有限。底层必须依赖数组，并且还要求数据是有序的。比较适合处理静态数据。
* 查找“值等于给定值”的场景，更多情况下回使用散列表或者二叉查找树，二分查找更适合用在“近似”查找问题，比如前面的情况2到6。

