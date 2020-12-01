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

✨ 坚持一月吃顿火锅儿！✨

#### 第二周（11-30 至 12-06）

**A:**

题目：[多数元素](https://leetcode-cn.com/problems/majority-element/)

很容易想到的是排序取中位数，这个是 O(nlog(n)) 的，更好的解决办法是摩尔投票法，即对拼消耗，O(n) 时间复杂度。

```java
class Solution {
    public int majorityElement(int[] nums) {
        int result = nums[0];
        int mod = 1;
        for (int i = 1; i < nums.length; i++) {
            if (result == nums[i]) {
                mod++;
            } else if (--mod == 0) {
                result = nums[i];
                mod = 1;
            }
        }
        return result;
    }
}
```

题目：[搜索二维矩阵 ||](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)

同简单，从二维矩阵的右上角开始找，比目标值大就往左查询，小就往下。

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int i = 0, j = matrix[0].length - 1;
        while (i < matrix.length && j >= 0) {
            int num = matrix[i][j];
            if (num < target) {
                i++;
            } else if (num > target) {
                j--;
            } else {
                return true;
            }
        }
        return false;
    }
}
```

**R:**

[How to display your Android project dependency graph in your README file](https://medium.com/google-developer-experts/how-to-display-your-android-project-dependency-graph-in-your-readme-file-e52dcadafa7a)

这篇文章讲的是可以在你的 Github README 文件里面展示 Android 项目的依赖关系图。其实核心就是写了一个 Gradle Task，生成了一个 .dot 文件，dot 即图片描述语言，脚本中还涉及把 .dot 文件转化成 png 的操作，用到的是 Graphviz，在安装完这个软件之后，还需要你的 AS 里面安装一个 State Art 插件，然后就可以直接运行该 Task 了。

**T:**

如何提升 TCP 三次握手的性能？

TCP 是一个可以双向传输的全双工协议，所以需要经过三次握手才能建立连接。三次握手在一个 HTTP 请求中的平均时间占比在 10% 以上，在网络状况不佳、高并发或者遭遇 SYN 泛洪攻击等场景中，如果不能正确的调整三次握手中的参数，就会对性能有很大的影响。

首先看客户端的优化，客户端在发出 SYN 报文后但没有收到对端 ACK 时，客户端会重发 SYN，重试的次数由 tcp_syn_retries 参数控制，默认是 6 次：

```
net.ipv4.tcp_syn_retries = 6
```

第 1 次重试发生在 1 秒钟后，接着会以翻倍的方式在第 2、4、8、16、32 秒共做 6 次重试，最后一次重试会等待 64 秒，如果仍然没有返回 ACK，才会终止三次握手。所以说总耗时是 127 秒，超过 2 分钟。

如果这是一台有明确任务的服务器，就可以根据网络的稳定性和目标服务器的繁忙程度修改重试次数，调整客户端的三次握手时间上限。比如内网中通讯时，就可以适当调低重试次数，尽快的把错误暴露给应用程序。

再看服务端的优化，服务端在收到 SYN 报文并回复 SYN+ACK 时，此时服务端会把该连接信息放入一个 SYN 版连接队列里面，当这个队列溢出时，服务端将无法在建立新的连接。所以此时可以修改 SYN 半连接队列的大小的，即：

```
net.ipv4.tcp_max_syn_backlog = 1024
```

如果 SYN 半连接队列已满，只能丢弃连接吗？并不是这样，开启 syncookies 功能就可以在不使用 SYN 队列的情况下成功建立连接。syncoookie 是这么做的：服务端根据当前状态计算出一个值，放在已方发出的 SYN+ACK 报文中发出，当客户端返回 ACK 报文时，取出该值验证，如果合法，就认为连接建立成功。

Linux 下怎样开启 syncookies 功能呢？修改 top_syncookies 参数即可，其中值为 0 时表示关闭该功能，2 表示无条件开启功能，而 1 则表示仅当 SYN 半连接队列放不下时再启用它。由于 syncookie 仅用于应对 SYN 泛洪攻击，这种方式建立的连接，许多 TCP 特性都无法使用，所以，应当把 tcp_syncookies 设置为 1，仅在队列满时再启用：

```
net.ipv4.tcp_syncookies = 1
```

当服务端发送完 SYN+ACK 报文后，但是却为收到对端 ACK，这时候服务端就会重发 SYN+ACK。当网络繁忙、不稳定时，报文丢失就会变严重，此时应该调大重发次数，反之则可以调小重发次数。对应的参数如下：

```
net.ipv4.tcp_synack_retries = 5
```

当服务端收到 ACK 后，内核就会把连接从 SYN 半连接队列中移除，再移入 accept 队列，等待进程调用 accept 函数时再把连接取出来。如果进程不能及时的调用 accept 函数，就会造成 accept 队列溢出，最终导致建立好的 TCP 连接被丢弃。

实际上，丢弃连接只是 Linux 的默认行为，我们还可以选择向客户端发送 RST 复位报文，告诉客户端连接已经建立失败。打开这一功能需要将 tcp_abort_on_overflow 参数设置为 1。

不过通常情况下，应该把该参数设置为 0，因为这样更有利于应对突发流量。

```
net.ipv4.tcp_abort_on_overflow = 0
```

举个例子，当 accept 队列满导致服务端丢掉了 ACK，与此同时，客户端的连接状态却是 ESTABLISHED，进程就会在建立好的连接上发送请求。只要服务端没有为请求回复 ACK，请求就会被多次重发。如果服务端上的进程只有短暂的繁忙导致 accept 队列满，那么当 accept 队列有空位时，再次接收到的请求由于含有 ACK，仍然会触发服务端成功建立连接。所以 tcp_abort_on_overflow 设为 0 可以提高连接建立的成功率，只有你非常肯定 accept 队列会长期溢出，才能设置为 1 以尽快通知客户端。

TFO 技术如何绕过三次握手？

TFO 即 TCP Fast Open，客户端可以在首个 SYN 报文中就携带请求，这节省了 1 个 RTT 的时间。TFO 具体是如何实现的呢？

为了让客户端在 SYN 报文中携带请求数据，必须解决服务端的信任问题。因为此时服务端的 SYN 报文还没有发给客户端，客户端是能够正常建立连接还未可知，但此时服务端需要假定连接已经建立成功，并把请求交付给进程去处理，所以服务端必须能够信任这个客户端。

TFO 到底怎样达成这一目的呢？它把通讯分为两个阶段，第一阶段为首次建立连接，这时走正常的三次握手，但在客户端的 SYN 报文会明确的告诉服务端它想使用 TFO 功能，这样服务端会把客户端 IP 地址用只有自己知道的密钥加密（比如 AES），作为 Cookie 携带在返回的 SYN+ACK 报文中，客户端收到后会将 Cookie 换存在本地。

之后，如果客户端再次向服务端建立连接，就可以在第一个 SYN 报文中携带请求数据，同时还要附带缓存的 Cookie。服务端收到后，会用自己的密钥验证返回 SYN+ACK。虽然客户端收到后还会返回 ACK，但服务器不等收到 ACK 就可以发送 HTTP 相应了，这就减少了握手带来的 1 个 RTT 的时间消耗。

小结一下，如何优化 TCP 的三次握手？我们可以从控制重发次数、调整连接队列的大小、开启 TFO 等思路去着手优化。

**S:**

[How to display your Android project dependency graph in your README file](https://medium.com/google-developer-experts/how-to-display-your-android-project-dependency-graph-in-your-readme-file-e52dcadafa7a)

[如何提升 TCP 三次握手的性能](https://time.geekbang.org/column/article/237612)

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

[单向散列函数：如何保证信息完整性?](https://time.geekbang.org/column/article/312846)
