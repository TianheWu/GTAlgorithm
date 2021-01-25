## Leetcode 127 单词接龙 题解

### 题目解读

该题目的大致含义为，给定一个起始单词和一个结束单词，和一些字典里的单词，一次只能由起始单词根据字典里的单词变一个字母，问最少需要变多少次才能变到结束的单词。如果无法变为结束的单词，就输出0。

### 解法：拆点法 + 广度优先搜索

理解完这道题的题意后，很明显我们会将本题构建成图的数据结构。然后对其进行搜索，找到最短路径。本题的难点在于一次只能变一个字母。因此大部分时间都需要思考，从该单词出发，下一步应该转移到哪一个单词。这里就利用到了一个小trick叫拆点法，我们将一个长度为N的单词拆成N + 1个点，从该单词引出边，连接向其余各点。

例如单词model，我们将其拆分成#odel, m#del, mo#el, mod#l, mode#, 然后将model与这五个点相连，我们将每一个单词进行这样的处理，然后构造无向图。将每两个相连的点进行连接这样，就能将所有相差一个字母的单词连接起来。最后利用队列，实现广搜，即可求得最小路径距离。

#### 代码

```c++
class Solution {
public:
    unordered_map<string, int> _map;
    vector<vector<int>> edges;
    int _idx = 0;
    void add_word(string s) {
        if (_map.find(s) == _map.end()) {
            _map[s] = _idx++;
            edges.emplace_back();
        }
    }
    void add_edge(string s) {
        add_word(s);
        int _i1 = _map[s];
        for (char& v:s) {
            char tmp = v;
            v = '#';
            add_word(s);
            int _i2 = _map[s];
            edges[_i1].emplace_back(_i2);
            edges[_i2].emplace_back(_i1);
            v = tmp;
        }
    }
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        for (string& s:wordList) 
            add_edge(s);
        add_edge(beginWord);
        if (_map.find(endWord) == _map.end())
            return 0;
        queue<int> q;
        q.push(_map[beginWord]);
        vector<int> dis(_idx, INT_MAX);
        int _s = _map[beginWord], _e = _map[endWord];
        dis[_s] = 0;
        while (!q.empty()) {
            int x = q.front();
            q.pop();
            if (x == _e)
                return dis[x] / 2 + 1;
            for (int& v:edges[x]) {
                if (dis[v] == INT_MAX) {
                    dis[v] = dis[x] + 1;
                    q.push(v);
                }
            }
        }
        return 0;
    }
};
```

#### 作者：欲扬先抑