## LeetCode 11. 盛水最多的容器

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/container-with-most-water

给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器，且 n 的值至少为 2。

<img src="https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg" alt="img" style="zoom:50%;" />

图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

**示例：**

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49
```

#### 方法一：暴力

计算出每两条垂直线内可容纳的水的体积，找出其中最多的那个，写两个for即可。

时间复杂度：O(N^2)

空间复杂度：O(1)

#### 方法二：双指针

这道题其实是可以用双指针的方法来实现时间复杂度为O(N)的。

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int left = 0, right = height.size() - 1;
        int ans = 0;
        while (left != right) {
            ans = max(ans, min(height[left], height[right]) * (right - left));
            if (height[left] <= height[right])
                left++;
            else right--;
        }
        return ans;
    }
};
```

我们首先要明确这里的双指针意味着什么。左指针和右指针分别指向目前容器左右的边界，利用这个两个指针，我们可以计算出目前指向的两个边界扩起来的水的体积。当前所盛的水的体积为：
$$
V=min(height[left], height[right]) * (right - left)
$$
现在我们该考虑怎么去移动左右指针，先给出结论，**每次更新完 ans 后要移动对应 height 更小的指针**。例如最开始height[left] = 1, height[right] = 7，计算出体积后，我们要令 left++。可以这样考虑这个例子，**根据所盛水的体积公式，体积是由两个量决定的，一个是 min(height[left], height[right])，另一个是 (right - left)**。 (right - left) 的值，只要移动，那么就会减小，min(height[left], height[right]) 如果要发生改变，只有当出现一个比 height[left] 更小的值。如果移动对应 height 更大的指针，那么此时 (right - left) 会减小，min(height[left], height[right]) 若要改变，那么只有height[right]变小，否则 min(height[left], height[right]) 不会改变，因此如果移动对应height更大的指针，我们的体积 V 便不会再变大了。因此，在移动指针的时候，我们要改变对应height更小的指针。

时间复杂度：O(N)

空间复杂度：O(1)