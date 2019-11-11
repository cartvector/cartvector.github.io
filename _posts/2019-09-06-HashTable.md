---
layout:     post
title:      HashTable
subtitle:   哈希表
date:       2019-09-06
author:     Alpha
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - 数据结构与算法
---





**[560. 和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)**

>



首先对于这类题，暴力的做法是嵌套循环，以每个元素为起始点遍历扫描，检查子数组和是否满足情况。时间复杂度为$ O(n^2)$，空间复杂度为$O(1)$。

进一步的优化思路：

首先：优化什么？

1. 目前时间复杂度高，空间复杂度是常数，所以优化点在时间上

2. 时间复杂度的优化都是通过空间来换取时间，而占用存储空间的数据结构应该满足常数时间查找

   然后：怎么优化？

1. 暴力算法中有没有重复运算？简单来看，有很多重复的加法运算。例如，在下标为0的时候，运算过 `nums[0] + nums[1] + nums[2]`，在下标为1的时候，又算了一次`nums[1] + nums[2]`
2. 找到重复运算后怎么消除重复运算？上例中，重复计算了`nums[1] + nums[2]`，换一种思路，实际上不需要加法，只需要 `nums[0] + nums[1] + nums[2] - nums[0]`，再写的清晰一些，用`sum[i]`表示从`0`到`i`所有元素的和，那么`nums[1] + nums[2] = sum[2] - sum[0]`。这里的`sum[i]`实际上就是一种前缀和的思路，只需要遍历一次就可以知道所有的前缀和，存在`map`里，用的时候就可以实现在常数时间的查找。
3. 有了大体的方向后，具体的map怎么存，key是什么，value是什么？对于这个问题来说，我们需要找到的是`target k`，即`sum[j] - sum[i] = k (j > i)`，`k`已知，`sum[j]`在迭代过程中逐步计算。需要存储的就只有`sum[i]`，查找`sum[i]`要常数时间，那么`map`的`key`应该是`sum[i]`。根据约束条件，value应该是当前值的下标。但是在实际实现的时候可以把这个约束隐藏在循环中，对于当前问题，要找到满足子数组的个数，`value`可以用来存储到当前位置，前缀和的个数，那么在找到满足的`sum[i] = sum[j] - k`的时候，最后的结果只需要加上`map[sum[j] - k]`即可。

[参考](https://leetcode-cn.com/problems/subarray-sum-equals-k/solution/c-nshi-jian-nkong-jian-xiang-jie-by-charon____/)

```C++
// 前缀和数组
// 时间复杂度O(n) 空间复杂度O(n)
class Solution {
public:
	int subarraySum(vector<int>& nums, int k) {
		unordered_map<int, int> m;  // (sum, count) 累计和计数
		m[0] = 1;// 注意这里前缀和多了一个0，防止漏掉数组的前缀和刚好等于k的情况
		int sum = 0;
		int ret = 0; // 和为k的子数组计数
		for (int i = 0; i < nums.size(); ++i){
			sum += nums[i];
			if (m.count(sum - k))
				ret += m[sum - k];
			m[sum] += 1;
		}
		return ret;
	}
};

int main()
{
	vector<int> a = { 1, 2, 3, 0，4, 5, 6, 7, 2, 9 };

	int res = Solution().subarraySum(a, 9);
	cout << res << endl;
	system("pause");
	return 0;
}
```

```
map的k = sum[i]   map的value    ret
0                    1          0
1                    1
3                    1
6                    2          +2
10                   1          +1
15                   1
21                   1          +1
28                   1
30                   1          +1
39                   1
                               = 5
```

