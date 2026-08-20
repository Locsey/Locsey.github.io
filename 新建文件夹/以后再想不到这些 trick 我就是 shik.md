以后再想不到这些 trick 我就是 shik

1. 平面图转对偶图：将所有线段极角排序后，对每个线段 $(u, v)$ 找到第一个 $< (v, u)$ 的边设为 $pre_u$，然后形成若干个环，其中存在一个外部节点，通过计算面积判断。[https://www.luogu.com.cn/problem/P3249](HNOI2016] 矿区)

2. LCA 不一定要倍增求，当树的形态比较特殊时可以模拟倍增的过程。[https://www.luogu.com.cn/problem/CF1491H](Yuezheng Ling and Dynamic Tree)，[https://www.luogu.com.cn/problem/P3248](P3248 [HNOI2016] 树)

3.  把 $m$ 个位置分配给 $n$ 个人，每个人有一个目标位置和初始方向，求方案数？

   新加一个 $m+1$ 点表示非法点，把 $m+1$ 个点串成一个环，对于一个点未被分配的概率是 $\dfrac{m+1 - n}{m+1}$  ，总方案为 $(m+1)^n2^n$，因此 $(m+1)^{m - 1}2^n(m +1 - n)$。

   [https://www.luogu.com.cn/problem/CF838D](Airplane Arrangements)

   定义一种合法方案的权值为所有人移动的步数和，再计数？

   对每个人分开考虑，设 $dp_n$ 表示 $n$ 个人 $n$ 个位置的答案，为 $(n+1)^{n - 1}2^{n} = (n+1)\sum\limits_{i = 1}^{n}\dbinom {n - 1}{i - 1}dp_{i - 1}dp_{n - i}$，再设 $dp_2$ 表示权值。$g_{n, m}$ 表示 $n$ 个人 $m$ 个位置的答案，转移枚举**极长选择后缀**， $= \sum\limits_{l = 0}^{m}g_{n - l, m - l - 1} dp_{l}$，再设 $g_{2, i, j}$ 表示权值。

   [https://www.luogu.com.cn/problem/CF1439D](INOI Final Contests)

4. 若要动态维护加边的 MST，当加的边都在一个方向时，可以只维护最后一排点的虚树，将路径上的最大值和新加入的边一起跑 Kruskal [世界地图]([P5360 [SDOI2019\] 世界地图 - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/P5360))

5. 当计数对象可以映射到多个对象时，考虑找到 “特殊点”（例如格路计数强制钦定走到 0，字典序最大最小等）。[AGC056B]([[AGC056B\] Range Argmax - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/AT_agc056_b))，[AGC013D]([[AGC013D\] Piling Up - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/AT_agc013_d))

6. $x \mathrm{\ and \ } y = 0$ 的点 $(x, y)$ 在二维平面上构成了树形结构。

   ```
   ################
   # # # # # # # #
   ##  ##  ##  ##
   #   #   #   #
   ####    ####
   # #     # #
   ##      ##
   #       #
   ########
   # # # #
   ##  ##
   #   #
   ####
   # #
   ##
   #
   ```

7. $n$ 个点中指定 $k$ 个点为根形成的 $k$ 个森林的方案 $kn^{n - k - 1}$。[樟]([Problem 8280 (bashu.com.cn)](https://oj.bashu.com.cn/code/problempage.php?problem_id=8280))

   [ARC155F]([[ARC155F\] Directable as Desired - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/AT_arc155_f))

8. 一棵树上 $n$ （偶数）个点两两匹配，代价为距离。以重心为根，最小匹配代价和最大匹配代价分别为 $\sum\limits_{x} sz_x \bmod 2, \sum\limits_{x} sz_x$，且所有在范围内且奇偶性相同的代价都可以构造出来，就是从大到小调整。[CF1396E](Distance Matching)

9. 一些消除 / 替换 ABC 的结论：

   - 金字塔形，$A = 0, B = 1, C = 2$，每次操作 $(A, B) \to - (A+B)$。
   - `ABC` $\to$ `ABC, BCA, CAB`：$a_i = s_i - i \bmod 3$，每次操作是选择长度为 3 的相同的连续段赋值。

10. wqs 二分该咋写？当有相同斜率应该取最小 / 最大的，根据题目要求而定。

11. 大多数 $(val, cnt)$ 形 dp，当完全包含更优时，可以让一维单调，另一位也单调，性质好时甚至可以直接平衡树维护 [To Play or not to Play]([To Play or not to Play - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/CF856F))

12. 一般图上最小点覆盖也等于点数减去最大独立集。[Algebra Flash]([Algebra Flash - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/CF1767E))

13. 

vlxxtv.net

flav-416/418

神楽ももか 佐山爱 桐谷茉莉 姬咲华 星乃麻美

bmw-299 waaa-433 Ghostds阿鬼 nide_xiaogou x.20510000.xyz

avfan.com

 sextb.cc

wanwan-baobei selen-S

girlsinprivates.com  hypestat.com

亚哈Ahab



https://en.jrants.com/huge-breast

https://en.jrants.com/cute-little-girl-in-fifth-dress-p5

[Search Results for “nudity only” – Jrants Pictures](https://en.jrants.com/?s=nudity+only)

ozin007

 Girlstar