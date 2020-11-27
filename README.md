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

[20 Things Most People Learn Too Late In Life](https://medium.com/better-advice/20-things-most-people-learn-too-late-in-life-23674cdbd75c)

大多数人在一生中学的比较晚的二十件事，比较有深刻印象的是以下几条：

1. Spontaneity is the sister of creativity.

   自发力是创造力的姐妹。做事情需要自己主动，我也那种很烦别人 push 我的那种。

2. Most people don't do what they love.

   大多数人都在做他们不喜欢做的事。的确是，生为一个打工人就应该有打工人觉悟（狗头）。作者想表达的意思其实是 “群众” 是那些没有过上自己想要的生活，他们没有足够的努力。当随着年纪的增长，环顾四周，和他们一样的人越多也就越早相信大家都是一样的。

3. Many stop reading after college.

   很多人在大学后就停止阅读了。害，现在印象深刻的读完的一本书还是去年读的初小轨的《自律的人生不孤独》，一本读起来很放松的书。今年马马虎虎看完了几本技术书，课外书基本没怎么看过。现在跳槽了，在当前公司更是时间被严重压缩，还是需要给自己挤出时间放空自己，读书就是一个很好的方式。

   而且我发现，现在越来越少的人会去看文章了，我在上大学那会，掘金、简书也还是比较火的平台，也会有不少人去写技术博客，去看去评论。现在再去看，已经明显减少了，一篇文章五十个赞都算多的。我想这其中也少不了 996 的干系。

4. You potential is directly correlated to how well you know yourself.

   你有多了解自己直接关系到你的潜力。这句话不能在赞同了，知道自己的劣势勤能补拙，知道自己的优势最大发挥。

**T:**

分享今天学到的一个知识点：单向散列函数。

什么是单向散列函数呢？

其实可以拆分为两部分讲，单向函数是指正向计算容易，逆向运算困难的函数。也就是说，给你一个输入，你能够很容易计算出输出，但是给你输出，你却很难计算出输入。在《应用密码学》有一个很生动的例子来解释单向函数。把盘子打碎很容易，但是再把这些碎片拼接成一个完整的盘子就是一件非常困难的事情了。

散列函数又叫哈希函数，是一个可以把任意大小的数据，转化成固定长度的数据（哈希值）。好的散列函数的是碰撞困难且散列值均匀分布。

那么单向散列函数就是既是一个单向函数又是一个散列函数，它最要紧的有两点，逆向运算困难和构造碰撞困难。单向散列函数有一个雪崩效应的特点，即微小的输入数据变化就能导致输出数据的巨大变化。

单向散列函数能够解决完整性问题。

**S:**

[20 Things Most People Learn Too Late In Life](https://medium.com/better-advice/20-things-most-people-learn-too-late-in-life-23674cdbd75c)

[单向散列函数：如何保证信息完整性?](
