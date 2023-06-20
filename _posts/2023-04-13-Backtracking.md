---
layout:            post
title:             "BackTracking"
date:              2023-04-14
tags:              Algo
category:          Algorithm
author:            hongyuan

---

## General approach

* Process all the paths starting from the root node(DFS):
    - If the current path has a valid answer, pick it
    - If reaching the end of a path, return
    - For each of the children nodes:
        * If the node is valid:
            - push the node
            - process all its children (DFS)
            - pop the node


```cpp
auto backtracking() {
    dfs( root );
    return ans;
}

void dfs() {
    if ( valid_ans ) {
        ans.push_back( ans_from_cur_path );
    }
    if ( end_of_path ) {
        return;
    }
    for ( auto node : nodes ) {
        if ( valid_node ) {
            cur_path.push_back( node );
            dfs( node );
            cur_path.pop_back( node );
        }
    }
}
```

## Examples

### [Leetcode 78. Subsets](https://leetcode.com/problems/subsets/description/)

![subset.svg]({{ site.github.url }}/res/2023-04-13-Backtracking/subset.svg#middle)

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        dfs(nums, 0);
        return ans;
    }

    void dfs(const vector<int>& nums, const int idx) {
        // Each node is a valid answer
        ans.push_back(cur);
        for (int i=idx; i<nums.size(); i++) {
            // Fix nums[i]
            cur.push_back(nums[i]);
            // Find all subsets for nums[i+1:]
            dfs(nums, i+1);
            // Backtracking
            cur.pop_back();
        }
    }

    vector<vector<int>> ans;
    vector<int> cur;
};
```

### [Leetcode 90. Subsets II](https://leetcode.com/problems/subsets-ii/description/)

![subset2.svg]({{ site.github.url }}/res/2023-04-13-Backtracking/subset2.svg#middle)

```cpp
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        dfs(nums, 0);
        return ans;
    }

    void dfs(const vector<int>& nums, const int idx) {
        // Each node is a valid answer
        ans.push_back(cur);
        for (int i=idx; i<nums.size(); i++) {
            // Filter duplicates
            if (i==idx || nums[i] != nums[i-1]) {
                // Fix nums[i]
                cur.push_back(nums[i]);
                // Find all subsets for nums[i+1:]
                dfs(nums, i+1);
                // Backtracking
                cur.pop_back();
            }
        }
    }

    vector<vector<int>> ans;
    vector<int> cur;
};
```

### [Leetcode 22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/description/)

![parenthness.svg]({{ site.github.url }}/res/2023-04-13-Backtracking/parenthness.svg#middle)

```cpp
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        dfs(n, 0, 0);
        return ans;
    }
    
    void dfs(const int n, const int nL, const int nR) {
        if ( nL == n ) {
            // All left backets are used, produce a valid answer by appending ')'
            ans.push_back( cur + string(n - nR, ')') );
            return;
        }

        // Filter: right node is only valid when nL > nR
        if ( nL > nR  ) {
            // Fix ')'
            cur.push_back(')');
            // Find all remaining combinations
            dfs(n, nL, nR+1);
            // Backtracking
            cur.pop_back();
        }

        // left node is always valid
        // Fix '('
        cur.push_back('(');
        // Find all remaining combinations
        dfs(n, nL+1, nR);
        // Backtracking
        cur.pop_back();
    }
        
    vector<string> ans;
    string cur;
};
```

### [Leetcode 46. Permutations](https://leetcode.com/problems/permutations/description/)

![permutations.drawio.svg]({{ site.github.url }}/res/2023-04-13-Backtracking/permutations.svg#middle)


```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        dfs( nums, 0 );
        return ans;
    }
    
    void dfs(vector<int>& nums, int idx) {
        if ( idx == nums.size() - 1 ) {
            // Valid answer is found when reaching the end of nums
            ans.push_back( nums );
            return;
        }
        for ( int i=idx; i<nums.size(); i++ ) {
            // Fix nums[0:idx] after swapping
            swap( nums[i], nums[idx] );
            // Find all remaining permutations
            dfs( nums, idx + 1 );
            // Backtracking
            swap( nums[i], nums[idx] );
        }
    }
    
    vector<vector<int>> ans;
};
```
