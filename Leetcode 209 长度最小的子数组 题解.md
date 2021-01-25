# LeetCode 209 长度最小的子数组 题解

## 思路：

### 方法一 暴力法：

暴力法，就是写两个for,统计从每一个数字开始的最短距离同时更新最小ans。

```c++
class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        int n = nums.size();
        if (n == 0) {
            return 0;
        }
        int ans = INT_MAX;
        for (int i = 0; i < n; i++) {
            int sum = 0;
            for (int j = i; j < n; j++) {
                sum += nums[j];
                if (sum >= s) {
                    ans = min(ans, j - i + 1);
                    break;
                }
            }
        }
        return ans == INT_MAX ? 0 : ans;
    }
};
```

时间复杂度：**O(N)**

空间复杂度：**O(1)**

### 方法二 双指针：

定义begin, end指针，并计算当前和sum, 每当出现sum >= s时，将begin指针向前移动，直到sum < s，同时更新ans。由于begin和end每个最多走n，因此时间复杂度为O(N).



## 代码：

```c++
class Solution {
public:
    int minSubArrayLen(int s, vector<int>& nums) {
        int ans = INT_MAX;
        int begin = 0, end = 0, sum = 0;
        if (nums.size() == 0)
            return 0;
        while (end < nums.size()) {
            sum += nums[end];
            while (sum >= s) {
                ans = min(ans, end - begin + 1);
                sum -= nums[begin];
                begin++;
            }
            end++;
        }
        return begin == 0 ? 0 : ans;
    }
};
```



## 复杂度分析：

时间复杂度：**O(N)**

空间复杂度： **O(1)**