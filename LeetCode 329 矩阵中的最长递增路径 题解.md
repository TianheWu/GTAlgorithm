# LeetCode 329 矩阵中的最长递增路径 题解

## 方法：记忆化深度优先搜索

如果你和我一样是个小白，对记忆化不太理解，希望你耐心读完这篇文章，保证你对记忆化有一个基本的认知。

这道题一开始我用普通的深搜，从每一个点进行遍历算长度，最后取最大，到第135个测试用例的时候超时了。然后想起了可以利用记忆化。其实记忆化的本质就是将已经计算过的结果记录下来，当下次搜索到该位置时直接返回该处的值，避免重复计算，利用空间换取时间。例如我下方代码的memo二维数组，其实就是新建的记忆化数组。

```c++
class Solution {
public:
    int dfs(vector<vector<int>>& memo, vector<vector<int>>& matrix, int i, int j) {
        if (memo[i][j] != 0)
            return memo[i][j];
        memo[i][j]++;
        if (i - 1 >= 0 && matrix[i - 1][j] > matrix[i][j])
            memo[i][j] = max(memo[i][j], dfs(memo, matrix, i - 1, j) + 1);
        if (i + 1 < matrix.size() && matrix[i + 1][j] > matrix[i][j])
            memo[i][j] = max(memo[i][j], dfs(memo, matrix, i + 1, j) + 1);
        if (j - 1 >= 0 && matrix[i][j - 1] > matrix[i][j])
            memo[i][j] = max(memo[i][j], dfs(memo, matrix, i, j - 1) + 1);
        if (j + 1 < matrix[0].size() && matrix[i][j + 1] > matrix[i][j])
            memo[i][j] = max(memo[i][j], dfs(memo, matrix, i, j + 1) + 1);
        return memo[i][j];
    }
    int longestIncreasingPath(vector<vector<int>>& matrix) {
        if (matrix.size() == 0)
            return 0;
        int ans = 0;
        vector<vector<int>> memo(matrix.size(), vector<int>(matrix[0].size(), 0));
        for (int i = 0; i < matrix.size(); i++)
            for (int j = 0; j < matrix[i].size(); j++)
                ans = max(ans, dfs(memo, matrix, i, j));
        return ans;
    }
};
```

记忆化的思想明确了，回到本题上来看看怎么应用这个思想。首先明确我们记忆化矩阵每一点存的值意味着什么。从深搜的角度入手，我们从i，j这个点出发，返回值为int意味着我们得到从该点出发能够经过的最长递增路径的值。举个一维数组的例子，现在是[1, 2, 3, 4]，我们从3这个点出发，找递增路径，然后求得从3出发的最大递增路径长度为2，现在从2出发，我们会深搜进3，再从3出发，到4，最后回到最开始的层，我们计算出的结果是3。这里就可以利用记忆化去减少时间，当我们从2出发时，到位置3，既然第一遍我们已经算过从3出发的路径长度，那么我们可以直接返回从3出发的路径长度，这样相当于直接是 1 + 从3出发的路径长度 = 3，现在将从2出发的路径长度记录下来存入memo[2]中，我们再从1出发，当1碰到2时，我们看看memo，发现从2出发的长度我们已经计算过了，那么我们不必要再去从2深搜，直接返回memo[2]的值即可。这便是记忆化的应用。

回到这个题上来，我们是不是也可以类比上面一维数组的例子来进行记忆化呢？
我们建立一个memo[][]二维数组，其中memo[i][j]意味着，从(i, j)这个点出发最长的递增路径长度。因为memo[i][j]最小为1，意味着此时这个点上下左右都没法走，它是上下左右最大的，那么最长也就是1了，这就是为什么代码中我每进入一层首先要给我的memo[i][j]++(我初始化将其全部为0了)。现在又有这样一个问题，每当你达到(i, j)这个位置的时候，你都要计算上下左右四个方向，你需要选四个方向中最长的路径长度将其记录进memo[i][j]，因此我在每个if里都进行了max(memo[i][j], dfs(...) + 1)进行比较，比如我先计算了上方存入memo，再计算左边，发现左边小于上面的memo，因此此时还保留上方的memo的值。当你读到这里时，你应该对记忆化有了一个基本的认知，并且明白这道题该怎么利用记忆化去做了。

## 复杂度分析：

时间复杂度：**O(mn)**，m和n分别为矩阵的行数和列数

空间复杂度：**O(mn)**，m和n分别为矩阵的行数和列数