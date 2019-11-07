---
layout:     post
title:      滑动窗口+HashTable
subtitle:   高级双指针 
date:       2019-09-06
author:     Alpha
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - 数据结构与算法
---



> 数据结构：`unordered_map`，即哈希表（字典）；
>
> `count(key)`：判断键是否存在；
>
> ` map[key]`：方括号访问键对应的值，如果该` key` 不存在，`C++` 会自动创建这个 `key`，并把` map[key] `赋值为 `0`。



**76. 最小覆盖子串**

```C++
class Solution {
public:
	string minWindow(string s, string t) {
		int start = 0, minLen = INT_MAX;
		int left = 0, right = 0;

		unordered_map<char, int> window;
		unordered_map<char, int> needs;
		for (char c : t) needs[c]++;

		int match = 0;

		while (right <= s.size()){
			char c1 = s[right];
			if (needs.count(c1)){
				window[c1]++;
				if (window[c1] == needs[c1])
					match++;
			}
			right++;     

			while (match == needs.size()){
				if (right - left < minLen){ 
			//此时right为匹配后的下一位，left为匹配为的第一位。
					start = left;
					minLen = right - left;   //left到right-1的长度
				}

				char c2 = s[left];
				if (needs.count(c2)){
					window[c2]--;
					if (window[c2] < needs[c2])
						match--;
				}
				left++;
			}
		}
		return minLen == INT_MAX ? "": s.substr(start, minLen);
	}
};
```





**438. 找到字符串中所有字母异位词**

```C++
class Solution {
public:
	vector<int> findAnagrams(string s, string p) {
		//用数组记录答案
		vector<int> res;
		int left = 0, right = 0;
		unordered_map <char, int> needs;
		unordered_map <char, int> windows;
		for (char c : p) needs[c]++;
		int match = 0;

		while (right < s.size()){
			char c1 = s[right];
			if (needs.count(c1)){
				windows[c1]++;
				if (windows[c1] == needs[c1])
					match++;
			}
			right++;

			while (match == needs.size()){
				//如果windows的大小合适，就把起始索引left加入到结果
				if (right - left == p.size()){
					res.push_back(left);
				}
				char c2 = s[left];
				if (needs.count(c2)){
					windows[c2]--;
					if (windows[c2] < needs[c2])
						match--;
				}
				left++;
			}
		}
		return res;
	}
};


```





**3. 无重复的最长子串**

```C++
class Solution {
public:
	int lengthOfLongestSubstring(string s) {
		int left = 0, right = 0;
		unordered_map<char, int> window;
		int res = 0; // 记录最长长度

		while (right < s.size()){
			char c1 = s[right];
			window[c1]++;
			right++;
			//如果window中出现重复字符，开始移动left缩小窗口。
			while (window[c1]>1){
				char c2 = s[left];
				window[c2]--;
				left++;
			}
			res = max(res, right - left);
		}
		return res;
	}
};
```



```C++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int curLength = 0;
        int maxLength = 0;
        
        /*int * position = new int[26];
        for(int i = 0;i<26;++i)
            position[i] = -1;         不只有26个字母  */  
        map<char,int>position;
        for(int i = 0;i<s.length();++i)
        {
            position[s[i]] = -1;
        }
        
        for(int i = 0;i<s.length();++i)
        {
            int prevIndex = position[s[i]];
            if(prevIndex < 0 || i-prevIndex > curLength)
                ++curLength;
            else
            {
                if(curLength > maxLength)
                    maxLength = curLength;
                
                curLength = i-prevIndex;
            }
            position[s[i]] = i;
        }
        if(curLength > maxLength)
            maxLength = curLength;
        
        //delete[] position;
        return maxLength;
    }
};
```





[参考](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/solution/hua-dong-chuang-kou-tong-yong-si-xiang-jie-jue-zi-/)