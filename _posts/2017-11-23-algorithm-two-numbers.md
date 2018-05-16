---
layout: post
title:  "两数求和相等的所有可能"
date:   2017-11-23 12:03:07 +0800
categories: algorithm
comments: true
math: true
author: ettingshausen
---  
![IMG_20171121_093946_949.jpg](//upload-images.jianshu.io/upload_images/1335634-c85b2f678a9f4ffe.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*2015年5月 海南大学*  
{:.image-caption}  

偶然发现一个算法题，不复杂，挺有意思的。

>问题：给一个数组，里面的数字不重复，求任意两数相加的和为一个固定值的所有可能的下标组合。  
 `使得时间复杂度最小`  
>例：{3, 5, 1, 2, 4, 6, 7}问求和为7的所有可能性
输出结果：[1,3],[0,4],[2,5]  

# 第一次尝试
---
不看答案，先尝试下自己的方案。 
思路： 遍历2遍数组，首先将循环的元素与目标值做差，并将结果保存下来，接着第二次循环从后边的元素中找

```java

int[] a = {3, 5, 1, 2, 4, 6, 7};
int target = 7;

List<Integer[]> result = new ArrayList<>();
List<Integer> tails = new ArrayList<>();
for (int i = 0; i < a.length; ++i) {
    if (tails.contains(a[i])) {
         continue;
    }
    int res = target - a[i];
    for (int j = i + 1; j < a.length; ++j) {
        if (a[j] == res) {
            Integer[] pair = new Integer[2];
            pair[0] = i;
            pair[1] = j;
            tails.add(a[j]);
            result.add(pair);
            break;
        }
    }
}

```
问题解决，只是几个数字根本看不出来耗时的差异。生成一个大的整数数组比较一下。
```java
Set<Integer> source = new HashSet<>();
int size = 50000;
for (int i = 0; i < size; i++) {
    Random random = new Random();
    source.add(random.nextInt(size) + 1);
}
int[] array = source.stream()
                    .mapToInt(i -> i)
                    .toArray();
```

通过 `System.currentTimeMillis()` 查看耗时，消耗了885毫秒，使用答案的方法耗时33毫秒。差异巨大！
过一眼答案的做法，只用了一次循环，并且用到了 `Map`。 分析下时间复杂度，如果一次循环，那么时间复杂度为 $$O(N)$$， 如果循环嵌套复杂度为 $$O(N^2)$$ 与实际耗时基本上一致。  

# 第二次尝试
---
根据刚才答案的提示，用一次循环搞定。
```java

List<Integer[]> getSum(int[] a, int target) {

    long begin = System.currentTimeMillis();
    List<Integer[]> result = new ArrayList<>();
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < a.length; i++) {
        if (map.get(a[i]) != null) {
            Integer[] pair = new Integer[2];
            pair[0] = map.get(a[i]);
            pair[1] = i;
            result.add(pair);
            continue;
        }
        int sub = target - a[i];
        map.put(sub, i);
    }
    long end = System.currentTimeMillis();
    System.out.println(end - begin);

    return result;
}

```  
效果非常惊艳，仅用了14毫秒，竟然比答案都要快。

# 对比分析
---
参考答案的代码如下：  

```java
List<Integer[]> twoSum(int[] a, int target) {
    long begin = System.currentTimeMillis();
    // key为当前下标对应的值和target的差值 value为当前下标
    Map<Integer, Integer> map = new HashMap<>();    
    List<Integer[]> result = new ArrayList<>(a.length);
    for (int i = 0; i < a.length; ++i) {
        int current = a[i]; // 当前下标对应的值
        Integer val = map.get(current);
        // 不存在差值则放入map
        if (val == null) {
            map.put(target - a[i], i);
        } else {
            // 存在差值,取出差值对应的下标和当前下标放入集合
            Integer[] each = new Integer[2];
            each[0] = map.get(current);
            each[1] = i;
            result.add(each);
        }
    }
    long end = System.currentTimeMillis();
    System.out.println(end - begin);
    return result;
}
```

通过代码来看，两个方法差不多，为什么会差一半的时间呢？经过反复的修改验证，发现 `getSum` 方法在 `twoSum` 方法之后调用，时间缩短了。交换顺序后，`twoSum` 消耗的时间也缩短了一半。看来，对同一个数组进行操作，系统自动做了优化。  

# 总结
---
减少时间复杂度，通常的做法就是通过牺牲点空间复杂度来换取。这算是个典型的例子，通过充分利用哈希表良好的时间复杂度，提升程序的性能。


# 参考资料  
---
[1] [算法练习02-两数求和相等的所有可能](http://benjaminwhx.com/2016/09/04/%E7%AE%97%E6%B3%95%E7%BB%83%E4%B9%A002-%E4%B8%A4%E6%95%B0%E6%B1%82%E5%92%8C%E7%9B%B8%E7%AD%89%E7%9A%84%E6%89%80%E6%9C%89%E5%8F%AF%E8%83%BD/)  
[2] [浅谈时间复杂度- 算法衡量标准Big O](http://www.cnblogs.com/chuanheliu/p/6389037.html)