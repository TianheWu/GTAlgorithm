# CODE FORCES Lonely day 题解

## 题目解读：

这道题的信息量比较大，咱们慢慢捋一下。给定N x M的网格，上面有起始位置'S'和结束位置'E'，'S'和'E'均为干净点可以穿行，中间有干净的点‘.'，是允许走的，有不干净的点'X'，是不允许走的，在这张二维图上有一些隧道，是允许角色穿行的，例如：

```
SXXX.
XXXXX
XXXXE
```

两个干净点之间如果中间全是'X'，且'X'的数量大于等于1，那么是可以通行的，并且通行距离为1。角色可以上下左右进行行走，对应为'UDLR'四个字母。题目最终让输出从位置S到位置E的最短路径长度，和由S走到E最短路径的最小字典序。

## 解题思路：

对于求图中两点的最短距离，并且四个方向都可以走，那么我们不能仅在这张二维网格中去进行深搜，我们需要根据二维网格中每两点之间是否能通行的关系，建立一个新图，这里我是建立了一个邻接表vector<int> G[MAX_VAL]表示每两个网格点之间的关系。题目中由于要输出最短距离最小字典序路径，同时我们要记录每两个点之间的边。其中边的定义如下：

```c++
struct Edge {
	int u, v;
	char c;
	Edge(int u_, int v_, char c_) : u(u_), v(v_), c(c_) {};
};
// u,v表示一条边起点和终点的标号，c代表这条边是上下左右哪个方向
// 对于每一个点的标号可以根据二维网格的下标index来定，例如grid[3][4]点的标号为3 * M + 4
```

所以我们第一件要做的事情，便是建立一张新的邻接表和边集，在建立边集的时候要给边赋上标号，而邻接表存的便是由边的标号，建立边集的代码如下：

```c++
void add_edge(int u, int v, char c) {
	edges.emplace_back(Edge(u, v, c)); // 边集存储边的信息
	int index = edges.size() - 1; // 边的标号
	G[u].emplace_back(index); // 邻接表存储信息：边的起点u，添加进去边的标号
}
```

建立完新图之后，我们考虑这样一个事情，求最短距离是很好求的，而且由于每两个点之间的距离都为1，因此不需要写Dijkstra算法，只需要一个广搜即可实现。那么难点就在于如何输出最小字典序，且保证是在最短距离的情况下。那么我们怎样克服掉这个难点呢，如果给定起点，我知道往四周哪一个点走，并且这个点在最短路径上，是不是就好了？那我们怎样让每一个点知道下一个点在最短路径上呢？

我们知道求起点到终点的最短路径和求终点到起点的最短路径是相同的，那么我们可以先求出终点到各个干净点的距离存在数组d[MAX_VAL]中，当我们再从起点往下一个点走的时候，假设起点为u，下一个点为v，我们只要判断这个起点到终点的距离比下一个点到终点的距离多1，那么就代表下一个点肯定在最短路径上（因为图中每两个能通行的点的距离为1），因此我们可以先反向广搜，得到终点到各个点的距离：

```c++
void rev_bfs() {
	memset(visited, false, sizeof(visited));
	queue<int> que;
	que.push(e_idx);
	visited[e_idx] = true;
	d[e_idx] = 0;
	while (!que.empty()) {
		int u = que.front();
		que.pop();
		for (int i = 0; i < G[u].size(); i++) {
			int e = G[u][i];
			int v = edges[e].v;
			if (!visited[v]) {
				visited[v] = true;
				d[v] = d[u] + 1;
				que.push(v);
			}
		}
	}
}
```

接着我们从起点开始走，根据字典序大小进行判断下一个点是否在最短路径上，即满足d[u] = d[v] + 1代码如下：

```c++
void bfs() {
	queue<int> que;
	que.push(s_idx);
	while (!que.empty()) {
		int ele = que.front();
		que.pop();
		int node = -1;
		char min_c = 'z';
		for (auto& v:G[ele]) {
			if (d[ele] - d[edges[v].v] == 1 && edges[v].c < min_c) {
				node = edges[v].v;
				min_c = min(min_c, edges[v].c);
			}
		}
		if (node != -1) {
			que.push(node);
			ans.emplace_back(min_c);
		}
	}
}
```

到这里这道题思路就通了，需要注意的是，这道题输入量很大，需要对cin进行加速操作，不然会超时：

```c++
std::ios::sync_with_stdio(false);
cin.tie(nullptr);
```

## 完整代码：

```c++
#include <bits/stdc++.h>
using namespace std;
 
const long long MAX_VAL = 4000009;
 
struct Edge {
	int u, v;
	char c;
	Edge(int u_, int v_, char c_) : u(u_), v(v_), c(c_) {};
};
 
int N, M;
int d[MAX_VAL];
pair<int, int> s_pos, e_pos; // 起点和终点的坐标
int s_idx, e_idx; // 记录起点和终点的标号
bool visited[MAX_VAL];
vector<char> ans;
vector<Edge> edges;
vector<int> G[MAX_VAL];
 
void add_edge(int u, int v, char c) {
	edges.emplace_back(Edge(u, v, c));
	int index = edges.size() - 1;
	G[u].emplace_back(index);
}
 
void rev_bfs() {
	memset(visited, false, sizeof(visited));
	queue<int> que;
	que.push(e_idx);
	visited[e_idx] = true;
	d[e_idx] = 0;
	while (!que.empty()) {
		int u = que.front();
		que.pop();
		for (int i = 0; i < G[u].size(); i++) {
			int e = G[u][i];
			int v = edges[e].v;
			if (!visited[v]) {
				visited[v] = true;
				d[v] = d[u] + 1;
				que.push(v);
			}
		}
	}
}
 
void bfs() {
	queue<int> que;
	que.push(s_idx);
	while (!que.empty()) {
		int ele = que.front();
		que.pop();
		int node = -1;
		char min_c = 'z';
		for (auto& v:G[ele]) {
			if (d[ele] - d[edges[v].v] == 1 && edges[v].c < min_c) {
				node = edges[v].v;
				min_c = min(min_c, edges[v].c);
			}
		}
		if (node != -1) {
			que.push(node);
			ans.emplace_back(min_c);
		}
	}
}
 
int main() {
	std::ios::sync_with_stdio(false);
	cin.tie(nullptr);
	cin >> N >> M;
	vector<vector<char>> grid(N, vector<char>(M));
	for (int i = 0; i < N; i++) {
		for (int j = 0; j < M; j++) {
			cin >> grid[i][j];
			if (grid[i][j] == 'S') {
				s_pos.first = i;
				s_pos.second = j;
			}
			else if (grid[i][j] == 'E') {
				e_pos.first = i;
				e_pos.second = j;
			}
		}
	}
 
	s_idx = s_pos.first * M + s_pos.second;
	e_idx = e_pos.first * M + e_pos.second;
	
	for (int i = 0; i < N; i++) {
		for (int j = 0; j < M; j++) {
			int cur_idx = i * M + j;
			if (grid[i][j] == 'X')
				continue;
			bool l = false, r = false, u = false, d = false;
			if (i - 1 >= 0 && (grid[i - 1][j] == '.' || 
                               grid[i - 1][j] == 'E' || grid[i - 1][j] == 'S')) {
				add_edge(cur_idx, (i - 1) * M + j, 'U');
				u = true;
			}
			if (i + 1 < N && (grid[i + 1][j] == '.' || 
                              grid[i + 1][j] == 'E' || grid[i + 1][j] == 'S')) {
				add_edge(cur_idx, (i + 1) * M + j, 'D');
				d = true;
			}
			if (j - 1 >= 0 && (grid[i][j - 1] == '.' || 
                               grid[i][j - 1] == 'E' || grid[i][j - 1] == 'S')) {
				add_edge(cur_idx, i * M + j - 1, 'L');
				l = true;
			}
			if (j + 1 < M && (grid[i][j + 1] == '.' || 
                              grid[i][j + 1] == 'E' || grid[i][j + 1] == 'S')) {
				add_edge(cur_idx, i * M + j + 1, 'R');
				r = true;
			}
 
			for (int k = i; k < N && (!d); k++) {
				if ((grid[k][j] == '.' || grid[k][j] == 'S' || 
                     grid[k][j] == 'E') && k - i > 1) {
					add_edge(cur_idx, k * M + j, 'D');
					break;
				}
			}
			for (int k = i; k >= 0 && (!u); k--) {
				if ((grid[k][j] == '.' || grid[k][j] == 'S' || 
                     grid[k][j] == 'E') && i - k > 1) {
					add_edge(cur_idx, k * M + j, 'U');
					break;
				}
			}
			for (int k = j; k < M && (!r); k++) {
				if ((grid[i][k] == '.' || grid[i][k] == 'S' || 
                     grid[i][k] == 'E') && k - j > 1) {
					add_edge(cur_idx, i * M + k, 'R');
					break;
				}
			}
			for (int k = j; k >= 0 && (!l); k--) {
				if ((grid[i][k] == '.' || grid[i][k] == 'S' || 
                     grid[i][k] == 'E') && j - k > 1) {
					add_edge(cur_idx, i * M + k, 'L');
					break;
				}
			}
		}
	}
	rev_bfs();
	bfs();
	if (d[s_idx] == 0)
		cout << -1;
	else {
		cout << d[s_idx] << "\n";
		for (int i = 0; i < ans.size(); i++) 
			cout << ans[i];
	}
	return 0;
} 
```


