## Codeforces Magic spells 题解

### 题目解读

本题的大致含义为给定一个字符串s，给定n个输入字符串，输出每个给定字符串中与原字符串s能按顺序匹配的字符，如果没有字符可以匹配的话，则输出‘IMPOSSIBLE’.

### 解法：桶排序思想 + 空间换时间思想

由于输出的字符串是根据原字符串位置决定的，因此我们可以先建立26个桶，每个桶代表一个字母，再在这个字母里面添加该字母出现的位置，可以这样实现：

```c++
for (int i = 0; i < S.size(); i++) {
    int idx = S[i] - 'a';
    mem[idx].emplace_back(i);
}
```

接下来我们的大体思路是：依次读取字符，查询对应字符桶中字符所在原始串s中位置，在读取下一个字符的时候要查看该字符的位置是否在上一个字符的后面，因此我们这里要定义一个变量pre_idx用来记录上一个字符在原字符串中出现的位置。如果该字符在上一个字符的后面的话，则添加进结果串print_s中，否则break出循环。这一部分都是利用桶排序的思想，实现记录字符位置的过程。

但是这里存在一个问题，每次我们查找相应字符的时候，都要从桶的开头处往后遍历，因此每一次的查找时间为O(m)，m为该字符出现的次数。因此，我们为了节省查找时间开销，还需要建立新的数组rem以此来记录读取到之前读过的字符出现的位置，也就是例如我之前读到字符a的位置是2，那么我将它记录下来，下一次查找的时候我直接从2处开始查找，实现O(1)时间复杂度进行查询，这样开辟了新的空间，但节省了时间。如果不进行该操作，将在test24处超时。

#### 代码

```c++
#include <bits/stdc++.h>
using namespace std;
 
string S, s;
int N;
vector<vector<int>> mem;
 
 
int main() {
	cin >> S >> N;
	mem.resize(26);
	for (int i = 0; i < S.size(); i++) {
		int idx = S[i] - 'a';
		mem[idx].emplace_back(i);
	}
	for (int i = 0; i < N; i++) {
		vector<int> rem(26, 0); // 26个字母对应桶里查询的下标 
		cin >> s;
		rem.resize(26, 0);
		string print_s;
		int pre_idx = -1;
		for (auto& c:s) {
			bool flag = false;
			int idx_2 = c - 'a';
			if (mem[idx_2].size() == 0)
				break;
			for (int k = rem[idx_2]; k < mem[idx_2].size(); k++) {
				int v = mem[idx_2][k];
				if (v > pre_idx) {
					print_s += c;
					pre_idx = v;
					rem[idx_2] = k;
					flag = true;
					break;
				}
				
			}
			if (!flag)
				break;
		}
		if (print_s.size() != 0)
			cout << print_s << "\n";
		else cout << "IMPOSSIBLE" << "\n";
	}
	return 0;
}
```

#### 复杂度分析：

时间复杂度：O(N^2)，遍历每个字符，同时去桶里找字符

空间复杂度：O(MN)，对每个字符进行空间上的开销

#### 作者：欲扬先抑