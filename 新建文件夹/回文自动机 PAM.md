## 回文自动机 PAM

回文自动机全称 Palindromic Automaton，用于处理回文串问题。用处相对于前两者而言很小。

### 基本定义和引理

- **节点**：原字符串中每个本质不同的回文串为一个节点，有 $len_x$ 保存当前串的长度。

- **转移边 trans**：一个节点状态的一个转移 $tr(x, c)$ 表示在 $x$ 两边同时加上字符 $c$ 得到的新的回文串。因此 $len_{tr(x,c)} = len_x+2$。

- **fail 指针**：类似于 SAM，PAM 的 fail 指针 $fail_x$ 指向的是 $x$ 最长的回文后缀。例如 ababa 指向的是 aba。从一个点 $x$ 不断跳 $fail_x$ 会得到以它作为结尾的回文子串的个数。
- **起始节点 st**：由于回文串分为奇回文串和偶回文串，trans 的转移不会改变字符串的奇偶性，因此 PAM 中有奇数根 $odd$ 和偶数根 $even$。为了方便，定义 $len_{odd} = -1$，$len_{even} = 0$。 

**引理 1**：字符串 $s$ 本质不同回文串个数为 $O(|s|)$ 级别。 

解释：数学归纳法。

- 当 $|s| = 1$ 时显然成立。
- 当 $|s|>1$ 时，设 $s = tc$，则考虑以 $c$ 结尾的所有回文子串，若其不为最长的显然可以通过以最长的翻转得到，因此最多会增加 1 个，为最长的回文子串。

### 构建 PAM

**算法 1**（依靠势能）：

使用增量法。初始化两个根 $odd = 1$ 和 $even  =0$，$fail_{0} = fail_{1} = 1$，记录当前插入的最后一个字符对应的节点 $lst$。

插入 $c$ 时，需要寻找以 $c$ 为结尾的最长回文子串作为当前节点。从 $lst$ 开始不断跳 $fail$ 直到 $str[n] = str[n - len_x - 1]$，设为 $p$，$q = tr_{q, c}$。若 $q$ 不存在则新建，否则将 $sz_q$ 更新后直接退出。然后先更新 $q$ 的 $fail$，再令 $tr_{p, c} = q$，寻找 $fail_q$ 可以从 $fail_p$ 开始也继续跳直到 $str[n] = str[n - len_x - 1]$，然后 $fail_q = x$。 

正确性显然。

时间复杂度用势能分析，每次跳 fail 都会使势能 -1，而插入一个字符最多使势能 + 1，因此线性。

【模板】回文自动机代码如下：

```
	int tot = 1, lst = 1, n; 
	int fa[M], len[M], dep[M], ch[M][26];  
	char str[M]; 
	inline int find(int p, int l) {
	    while(str[l] != str[l - len[p] - 1]) p = fa[p]; 
	    return p; 
	}
	inline void ins(int n, int c) {
		int p = find(lst, n), q = ch[p][c]; 
		if(!q) q = ++tot, len[q] = len[p] + 2, fa[q] = ch[find(fa[p], n)][c], dep[q] = dep[fa[q]] + 1, ch[p][c] = q; 
		lst = q; 
	}
	inline void mian() {
		scanf("%s", str + 1), n = strlen(str + 1); 
		len[1] = -1, fa[0] = fa[1] = 1; int lans = 0; 
		for(int i = 1; i <= n; ++i) {
			if(i > 1) str[i] = char((str[i] - 97 + lans) % 26 + 97); 
			ins(i, str[i] - 'a'); 
			printf("%d ", lans = dep[lst]); 
		}
	}
```

**算法 2**（严格 $O(n|\sum|)$）：

考虑优化暴力跳 fail 的过程。本质上是寻找 $x$ 到根的链上第一个 $z$ 满足 $c = str[n - len_z - 1]$，因此可以预处理 $qc[x][c]$ 表示 $x$ 到根路径上的一个点表表示当前字符为 $c$ 时存在 $c$ 转移的点。

发现 $qc[x][c]$ 可以通过 $qc[fail_x][c]$ 继承得到，于是只用更改 1 个点的值即可。

代码：

```
	inline int find(int p, int l) {
		return str[l - len[p] - 1] == str[l] ? p : qf[p][str[l] - 'a']; 
	}
	inline void ins(int n) {
		int c = str[n] - 'a', p = find(lst, n), q = ch[p][c]; 
		if(!q) {
			q = ++tot, len[q] = len[p] + 2; int f = qf[p][c]; f = ch[f][c]; 
			dep[q] = dep[fa[q] = f] + 1, memcpy(qf[q], qf[f], sizeof(qf[f])); 
			qf[q][str[n - len[f]] - 'a'] = f; assert(f != q); 
			ch[p][c] = q; 
		}
		lst = q; 
	} 
	inline void mian() {
		scanf("%s", str + 1), n = strlen(str + 1); 
		len[1] = -1, fa[0] = tot = 1; 
		for(int i = 0; i < 26; ++i) qf[0][i] = 1; 
		for(int i = 1; i <= n; ++i) {
			if(i > 1) str[i] = char((str[i] - 97 + lans) % 26 + 97); 
			ins(i); 
			printf("%d ", lans = dep[lst]); 
		}
	}
```

由于不依赖势能，可以用来可持久化 / 在末尾增 / 删等操作。

##

#### 例题

##### 【APIO2014】回文串

显然，建出 PAM 后累加子树 siz，答案就是 $\max(len_i \times siz_i)$。 

##

##### 【hdu6599】我喜欢回文串I Love Palindrome String

此题要寻找半回文串，设 $x$ 节点对应的长度 $\leq \dfrac{len_x}2$，且最长的回文后缀为 $half_x$，只要保证 $half_x = \dfrac{len_x+1}2$ 即可累加 $siz_x$ 的答案，于是问题变为快速求 $half_x$。

由于 $half_x$ 和 $len_x$ 的相似性，我们仍然可以暴力求解，即根据转移前的节点的 $half$ 不断跳，直到满足条件即可。 

 ##

##### 【BZOJ4044】病毒的合成Virus synthesis

先对给定串建出 PAM，设 $dp_x$ 表示从空串得到 $x$ 的最小次数，答案就是 $\min(n - len_x+dp_x)$。 

对于一个操作序列而言，将每次的操作 2 分段，考虑最后一次操作 2，得到的一定是偶回文串。

所以我们只用考虑操作 2 之间的转移，也就是说，**长度为奇数的串可以忽略**。

求出 $half_x$，则 $x$ 的祖先 $y$ （$len_y \leq half_x$）的贡献是：$dp_y+\dfrac{len_x}2 - len_y +1$。 

若有 $y \to x$，则还可以从 $dp_y+1$ 转移过来。

初始化 $dp_{even} = 1$ 计算即可。 

##

##### 【BZOJ2565】最长双回文串

《Border 和回文后缀》应用 2。

##

##### 【BZOJ5384】有趣的字符串题

离线后扫描线，若对于每个回文串维护最靠右的位置，然后树状数组即可。

![](https://cdn.luogu.com.cn/upload/image_hosting/kdp4yuck.png?x-oss-process=image/resize,m_lfit,h_170,w_225)

如图所示，根据 《Border 和回文后缀》的内容，在同一个等差数列中，第一个串的贡献为其上一次出现位置 + 1 至当前位置，$fail_x$ 的贡献为第二个红色起始位置 + 1至黑色位置...。以此类推，总贡献为 $[lst_x- len_x+2, i - len_x+(dep_x - dep_{slink_x}-1) \times diff_x+1]$。而 $x$ 的所有出现位置等于其子树的出现位置，用线段树维护。 

##

##### 【GDKOI2013】 大山王国的城市规划

若两个回文串 $x,y$（$|x|<|y|$） 为包含关系，则 $x$ 可以通过走 fail 边，或者走 trans 边得到 $y$。 

所以建出这个图，是个 DAG，然后要求最长反链，根据 Dilworth 定理，等于最小链覆盖，跑网络流即可。 

##



## Border 理论

抄写于 金策《字符串算法选讲》。

补充定义：

- 定义正整数 $p$ 是串 $S$ 的**周期**，当且仅当 $p \leq |S|$ 且 $\forall i\in [1, |S| - p]$，$S_i = S_{i+p}$。若 $p$ 整除 $S$ 则称为 $p$ 是 $S$ 的整周期。 
- 定义正整数 $r$ 是串 $S$ 的 **border** 当且仅当 $\mathrm{pre}(s, r) = \mathrm{suf}(s, r)$。 

**推论 1：**$p$ 是 $S$ 的周期 $\Leftrightarrow |S| - p$ 是 $S$ 的 border。

**弱周期引理（Weak Periodicity Lemma）：**若 $p, q$ 是 $S$ 的周期，$p+q \leq |S|$，则 $\gcd(p, q)$ 也是 $S$ 的周期。

证明：设 $p < q$，$d = q - p$。则 $\forall i > q$，$S_i = S_{i - q} = S_{i - q +p}$。 

对于 $q - p +1 \leq i \leq q$，只要满足 $i+p \leq |S|$，同样有 $S_i = S_{i+p} = S_{i- q+p}$。 

利用数学归纳法归纳 $(q - p, p)$ 直到一方为 0，即是辗转相除法，因此 $ \gcd(p, q)$ 是周期。

**周期引理（Periodicity Lemma）** ：若 $p, q$ 是 $S$ 的周期， $p+q - \gcd(p, q) \leq S$，则 $gcd(p, q)$ 也是 $S$ 的周期。 

证明：好难，不会。

### **border 的结构**

**引理 3**：字符串 $u, v$ 满足 $2|u| \geq |v|$，则 $u$ 在 $v$ 出现的位置构成一个等差数列。 

证明：只用考虑至少出现 3 次的情况。 

![1 ](https://cdn.luogu.com.cn/upload/image_hosting/e5su38wh.png?x-oss-process=image/resize,m_lfit,h_170,w_225)

如图所示，设 $u_1, u_2 $ 为前两次出现的位置， $u_3$ 为任意一次出现位置。则 $d, q$ 都为 $u$ 的周期，因此 $r = \gcd(d, q)$ 也为 $u$ 的周期。设 $u$ 的最小周期为 $p \leq r$。 

因为 $p \leq r \leq q \leq |u_1 \cap u_2|$，因此 $p$ 也是 $u_1 \cap u_2$ 的周期，若 $p < d$，则会出现更靠前的匹配，因此 $p = d$。 

因此 $p = d \leq \gcd(d, q) \Rightarrow q \mid d$。 



**引理 4**：串 $s$ 的所有不小于 $\dfrac{|s|}2$ 的 border 组成一个等差数列。

证明：设 $s$ 最大 border 长度为 $n - p$，另外一个 border 长度为 $n - q$（$p, q \leq \dfrac{|s|}2$），则根据弱周期引理， $\gcd(p, q)$ 为 $s$ 的周期 $\Rightarrow \gcd(p, q) = p \Rightarrow q \mid p$。 

![](https://cdn.luogu.com.cn/upload/image_hosting/5cit67nv.png?x-oss-process=image/resize,m_lfit,h_170,w_225)

 ##

因此，将 $s[1...n]$ 的所有 border 按照长度分类后：$x \in [1, 2), [2, 4), \dots, [2^{k - 1}, 2^k), [2^k, n]$。

有两种情况：

- $x \in [2^k,n]$，已经讨论过这种情况。

- $x \in[2^{i - 1}, 2^i)$

  当 $|u| = |v|$ 时，设 $\mathrm{PS}(u, v) = \{k \ | \ \mathrm{pre}(u, k) = \mathrm{suf}(u, k\}$ ， $\mathrm{LargePS}(u, v) = \{k \ | \ k \in \mathrm{PS}(u,v), k \geq \dfrac{|u|}2  \}$ 。

  则 $[2^{i - 1}, 2^i)$ 内 border 长度集合为 $\mathrm{LargePS}(\mathrm{pre}(s, 2^i), \mathrm{suf}(s, 2^i))$。 

**引理 5** ：$\mathrm{LargePS}(u, v)$ 构成一个等差数列。

证明：设其中最大元素为 $x$，则剩下的元素都是 $x$ 的 border，根据引理 4 显然成立。

**定理 1**：串 $s$ 的所有 border 按照长度排序后，可以划分成 $\log |s|$ 个不交的段，每一段都是一个等差数列。 

### 子串周期查询

> 给定串 $s$，多次询问 $s[l,r]$ 的所有周期，用 $\log |s|$ 个等差数列表示。

Case1：当 $x \in [2^{i - 1}, 2^i)$，即计算 $\mathrm{LargePS}(\mathrm{pre}(t, 2^i), \mathrm{suf}(t, 2^i))$。

若 $u$ 是一个 $\mathrm{Large \ \  Prefix-Suffix}$，则 $\mathrm{pre}(t, 2^{i - 1})$ 是 $u$ 的前缀， $\mathrm{suf}(t, 2^{i - 1})$ 是 $u$ 的后缀。

求出 $\mathrm{pre}(t, 2^{i - 1})$ 在 $\mathrm{suf}(t,2^i)$ 的所有出现位置， $\mathrm{suf}(t, 2^{i - 1})$ 在 $\mathrm{pre}(t, 2^i)$ 的所有出现位置，，则 border 等于后者移位后取交集。

![](https://cdn.luogu.com.cn/upload/image_hosting/h2owrxjl.png?x-oss-process=image/resize,m_lfit,h_170,w_225)

而 $|v| \geq \dfrac{|w|}2$，$v$ 在 $w$ 中所有匹配位置构成了一个等差数列，于是只用将首项和公差求出来即可。 

即：求出 $v$ 在 $w$ 左边的第一，二次匹配和最后一次匹配。相当于实现一个 $\mathrm{succ}(v, i)$ 表示 $v$ 在原串的不小于 $i$ 的第一次匹配和反过来的 $\mathrm{pred}(v, i)$。

可以在倍增求后缀数组时把每一轮结束后的结果都记录下来。

Case2：$x \in [2^k, r - l+1]$ ，做法一样。

于是问题变成如何对两个等差数列求交。

**引理 6**：四个串满足 $|x_1| = |y_1| \geq |x_2| = |y_2|$，且 $x_1$ 在 $y_2y_1$ 出现了至少 3 次， $y_2$ 在 $x_1x_2$ 出现了至少 3 次，则 $x_1$ 和 $y_1$ 的最小周期相等。

![](https://cdn.luogu.com.cn/upload/image_hosting/eg5z0rl2.png?x-oss-process=image/resize,m_lfit,h_170,w_225)

证明：反证法。不妨设 $per(x_1) > per(y_1)$，考虑 $x_1$ 在 $y_2y_1$ 中最靠右的一次匹配，设与 $y_1$ 重叠部分为 $z_1$ 。

则 $|z| \geq 2per(x_1) > per(x_1)+per(y_1)$，根据弱周期引理， $z$ 具有周期 $d = \gcd(per(x_1), per(y_1)) \mid per(x_1)$。 

于是 $d$ 也是 $x_1$ 的周期，但 $d < per(x_1)$，矛盾。

所以我们合并的两个等差数列要么长度 $\leq 3$，要么公差相等，所以可以 $O(1)$ 合并。

于是做到了 $O(n \log n) - O(\log^2n)$。

当然，可以继续对 $succ(v, i)$ 的计算优化做到 $O(n \log n) - O(\log n)$，不过上面的算法已足够。

#### 例题

##### P4156 [WC2016]论战捆竹竿

##

##### Loj#6681 yww 与树上的回文串

##

##### CF1286E Fedya the Potter Strikes Back

##

##### P5287 [HNOI2019]JOJO

###  Border 与回文后缀

**引理 1**：$s$ 是回文串，则 $t$ 是 $s$ 的 border $\Leftrightarrow$ $t$ 是回文串。

证明：显然。

**推论 1** ：串 $s$ 的所有回文后缀的长度可以表示成 $\log |s|$ 个等差数列。

证明：根据 border 的结构 定理 1 即可。

**应用 1**：最小回文拆分

> 将字符串 $s$ 分解成 $s = s_1s_2\dots s_k$，使得 $s_i$ 都是回文串，且 $k$ 最小。

设 $diff_x = len_x - len_{fail_x}$，$slink_x$ 表示 fail 树上距离 $x$ 最近的 $y$ 满足 $diff_y \neq diff_x$ 。

则从 $x$ 开始跳 $fail$，一直跳到 $y$ 之前，都是一个等差数列。 

![gd7acqi5.png (225×96) (luogu.com.cn)](https://cdn.luogu.com.cn/upload/image_hosting/gd7acqi5.png?x-oss-process=image/resize,m_lfit,h_170,w_225)

如图所示，$x$ 是当前的最长回文后缀，由于对称性，$fail_x$ 上一次出现的位置为红色区域 $x - diff_x$，而这恰好对应了 $x$ 的起始位置，同理第二个红色区域对应了当前 $fail_x$ 的位置，只剩下绿色区域没有计算，等于 $i - len_x+(dep_x - dep_{slink_x} - 1) \times diff_x$。然后不断跳 $slink$ 依次更新即可。 

**应用 2**：双回文串

> 若 $s = ab$，$a,b$ 都是回文串，则称 $s$ 是双回文串。现给定 $s$，求 $s$ 子串中最长双回文串长度。 

引理 2：若 $s = x_1 x_2=y_1 y_2= z_1 z_2$，且 $|x_1|<|y_1|<|z_1|$，$x_2, y_1, y_2, z_1$ 是回文串，则 $x_1, y_2$ 是回文串。

证明：略。

定理 2：若 $s$ 是一个双回文串，则存在一种拆分方式 $s = ab$，使得 $a$ 是 $s$ 的最长回文前缀，或 $b$ 是 $s$ 的最长回文后缀。 

所以枚举断点，求出一个最长回文后缀和最长回文前缀拼起来即可。

## Lyndon word

