---
layout:            post
title:             "BackTracking"
date:              2023-04-14
tags:              Algo
category:          Algorithm
author:            hongyuan

---

## General approach

```cpp
auto backtracking() {
    dfs();
    return ans;
}

void dfs() {
    if ( valid_ans ) {
        ans.push_back( ans_from_cur_path );
        return;
    }
    for ( auto node : nodes ) {
        if ( valid_node ) {
            cur_path.push_back( node );
            dfs(ans);
            cur_path.pop_back( node );
        }
    }
}
```

## Examples

https://leetcode.com/problems/subsets/description/

![subset.svg]({{ site.github.url }}/res/2023-04-13-Backtracking/subset.svg#middle#middle)

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        dfs(nums, 0);
        return ans;
    }

    void dfs(const vector<int>& nums, const int idx) {
        ans.push_back(cur);
        for (int i=idx; i<nums.size(); i++) {
            cur.push_back(nums[i]);
            dfs(nums, i+1);
            cur.pop_back();
        }
    }

    vector<vector<int>> ans;
    vector<int> cur;
};
```

https://leetcode.com/problems/subsets-ii/description/

![subset2.svg]({{ site.github.url }}/res/2023-04-13-Backtracking/subset2.svg#middle#middle)

```cpp
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        dfs(nums, 0);
        return ans;
    }

    void dfs(const vector<int>& nums, const int idx) {
        ans.push_back(cur);
        for (int i=idx; i<nums.size(); i++) {
            if (i==idx || nums[i] != nums[i-1]) {
                cur.push_back(nums[i]);
                dfs(nums, i+1);
                cur.pop_back();
            }
        }
    }

    vector<vector<int>> ans;
    vector<int> cur;
};
```

https://leetcode.com/problems/generate-parentheses/description/

![parenthness.drawio.svg]({{ site.github.url }}/res/2023-04-13-Backtracking/parenthness.svg#middle#middle)

```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        dfs(n);
        return ans;
    }
    
    void dfs(const int n) {
        if ( nL == n ) {
            // All left backets are used, produce a valid answer by appending ')'
            ans.push_back( cur + string(n - nR, ')') );
            return;
        }

        // right node is only valid when nL > nR
        if ( nL > nR  ) {
            cur.push_back(')');
            nR ++;
            dfs(n);
            nR --;
            cur.pop_back();
        }

        // left node is always valid
        cur.push_back('(');
        nL ++;
        dfs(n);
        nL --;
        cur.pop_back();
    }
        
    vector<string> ans;
    string cur;
    int nL; // number of left bracket
    int nR; // number of right bracket
};
```
