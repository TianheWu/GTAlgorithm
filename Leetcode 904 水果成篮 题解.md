# Leetcode 904 水果成篮 题解

## 思路：

这题是一道比较典型的双指针题，可以把它理解为寻找最长的子序列，且子序列中包含两种元素。因此我们可以想到移动right指针，当指向的元素已经超过原有的两种时，我们要更新left。因此我们的关键点在于如何确定更新left的位置。

举一个例子当数组是这样时：[2, 2, 2, 1, 3, 3, 3, 4, 4, 4, 4]当我们的right指针遍历到第一个4时，左指针left要指向第一个3而不是最后一个3；再举一个例子: [1, 2, 1, 1, 2, 1, 3, 3, 3, 3]当我们的right指针遍历到第一个3时，左指针left要指向最后一个1，而不是第一个1。

因此我们不仅要记录当前遍历时的种类数量，同时要记录在当前2种以内的情况下，是否出现了种类翻转，我们要记录种类翻转的最后一个， 也就是代码中else if实现的，并在selected_num > 2时赋值给left。可以利用哈希表记录出现的种类，当种类大于2时，将它及时清空，清空后添加更新后的tree[left]。



## 代码：

```c++
class Solution {
public:
    int totalFruit(vector<int>& tree) {
        int left = 0, right = 0, selected_num = 0;
        int ans = 0, tmp_pos = 0;
        unordered_map<int, int> map;
        while (right < tree.size()) {
            if (map.find(tree[right]) == map.end()) {
                selected_num++;
                if (selected_num > 2) {
                    map.clear();
                    selected_num = 2;
                    left = tmp_pos;
                    map[tree[left]]++;
                }
                tmp_pos = right;
            }
            else if (map.find(tree[right]) != map.end() && tree[right] != tree[right - 1])
                tmp_pos = right;
            map[tree[right]]++;
            ans = max(ans, right - left + 1);
            right++;
        }
        return ans;
    }
};
```



## 复杂度分析：

时间复杂度：因为是一次遍历所以时间复杂度为**O(N)**

空间复杂度：该题哈希表中只存2个值，因此空间复杂度为**O(1)**