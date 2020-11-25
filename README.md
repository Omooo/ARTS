---
ARTS
---

#### 目录

```
A: Algorithm，每周至少做一道 Leetcode
R: Review，阅读并点评至少一篇英文文章
T: Tips，学习至少一个技术技巧
S: Share，分享一篇有观点和思考的技术文章
```

✨ 坚持一周奖励自己一个鸡排吃！✨

#### 第一周（11-24 至 11-29）

**A:**

题目：[只出现一次的数字](https://leetcode-cn.com/problems/single-number/)

标签：位运算、哈希表

这道题比较简单，上来就能想到异或运算，两个相同的数异或为 0，任何数与 0 异或都是它本身。

```java
    public static int singleNumber(int[] nums) {
        int result = 0;
        for (int i : nums) {
            result ^= i;
        }
        return result;
    }
```

```kotlin
fun singleNumber(nums: IntArray): Int {
    var result = 0
    nums.forEach { i ->
        result = result xor i
    }
    return result
}
```

**R:**

**T:**

分享今天学到的一个知识点：单向散列函数。

什么是单向散列函数呢？

其实可以拆分为两部分讲，单向函数是指正向计算容易，逆向运算困难的函数。也就是说，给你一个输入，你能够很容易计算出输出，但是给你输出，你却很难计算出输入。在《应用密码学》有一个很生动的例子来解释单向函数。把盘子打碎很容易，但是再把这些碎片拼接成一个完整的盘子就是一件非常困难的事情了。

散列函数又叫哈希函数，是一个可以把任意大小的数据，转化成固定长度的数据（哈希值）。好的散列函数的是碰撞困难且散列值均匀分布。

那么单向散列函数就是既是一个单向函数又是一个散列函数，它最要紧的有两点，逆向运算困难和构造碰撞困难。单向散列函数有一个雪崩效应的特点，即微小的输入数据变化就能导致输出数据的巨大变化。

单向散列函数能够解决完整性问题。

**S:**

