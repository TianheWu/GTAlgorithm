# LeetCode 1004 最大连续1的个数 III 题解

## 思路：

这题是一道比较典型的滑动窗口思想的题，可以利用双指针对其求解。首先定义left，right指针，每当right指针扫到为1的时候，则令1的个数count_num_1++，同时不断更新当前最大1的个数max_current_num。只有当right - left + 1 - max_current_num > k 的时候才意味着，不能继续将0替换为1了。此时，要判断left指针对应的数，如果为1，就减一，不为1就不用减，并令左指针left++。最终窗口的长度即为答案。



## 代码：

```c++
class Solution {
public:
    int longestOnes(vector<int>& A, int K) {
        int ans = 0;
        int left = 0, right = 0;
        int count_num_1 = 0, max_current_num = 0;
        while (right < A.size()) {
            if (A[right] == 1)
                count_num_1++;
            max_current_num = max(max_current_num, count_num_1);
            if (right - left + 1 - max_current_num > K) {
                count_num_1 -= A[left] == 1;
                left++;
            }
            ans = max(ans, right - left + 1);
            right++;
        }
        return ans;
    }
};
```



## 复杂度分析：

时间复杂度：从左到右遍历一遍 **O(N)**

空间复杂度：利用常数空间进行存储 **O(1)**