# LeetCode LCP18 早餐组合 题解

## 方法一：排序 + 二分搜索

首先是最朴素的方法，我们可以统计每个staple和drinks进行匹配，看是否满足题意条件。

但是这道题如果单纯用双层for循环暴力会超时：

```c++
class Solution {
public:
    int breakfastNumber(vector<int>& staple, vector<int>& drinks, int x) {
        long long ans = 0;
        for (int i = 0; i < staple.size(); i++) {
            for (int j = 0; j < drinks.size(); j++) {
                if ((long long) staple[i] + (long long) drinks[j] <= (long long) x)
                    ans  = (ans + 1) % 1000000007;
            }
        }
        return ans;
    }
};
```

因此我们可以对第二层for进行优化，搜索一个数的话，我们可以利用二分法搜索的方法。但是需要先对staple和drinks排序。这种思路满足题目给定的时间复杂度，可AC。****

```c++
class Solution {
public:
    int breakfastNumber(vector<int>& staple, vector<int>& drinks, int x) {
        long long ans = 0;
        sort(staple.begin(), staple.end());
        sort(drinks.begin(), drinks.end());
        for (int i = 0; i < staple.size(); i++) {
            int left = 0, right = drinks.size() - 1;
            while (left <= right) {
                int p = (left + right) / 2;
                if (staple[i] + drinks[p] <= x) {
                    left = p + 1;
                }
                else right = p - 1;
            }
            ans += right + 1;
            ans = ans % 1000000007;
        }
        return ans;
    }
};
```

## 方法二：排序 + 双指针 + 贪心

这道题也可以利用双指针+贪心的思路，当对staple和drinks进行排序后，利用staple最小的值去匹配drinks最大的值，然后依次对right--，如果满足题目要求，那么 ans += right + 1。利用贪心的思想，再判断第二小的staple值和当前right去匹配即可。这里要注意，while的循环条件为right>=0，并且如果排序后满足最外侧staple[i] + drinks[right] <= x可以break。

## 代码：

```c++
class Solution {
public:
    const long long MOD = 1000000007;
    int breakfastNumber(vector<int>& staple, vector<int>& drinks, int x) {
        long long ans = 0;
        sort(staple.begin(), staple.end());
        sort(drinks.begin(), drinks.end());
        int right = drinks.size() - 1;
        for (int i = 0; i < staple.size(); i++) {
            while (right >= 0) {
                if (staple[i] + drinks[right] <= x) {
                    ans += right + 1;
                    ans = ans % MOD;
                    break;
                } else right--;
            }
        }
        return ans;
    }
};
```



## 复杂度分析：

时间复杂度：**O(nlogn)**

空间复杂度：利用常数空间进行存储 **O(1)**