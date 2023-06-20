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

Permutations is also a backtracking problem. Swap is used instead of push/pop.

Approach #1:

Red ones are the digits being swapped at each step. Push `nums` to the `ans` at leaf nodes. Backtracking is done at each iteration.

![permutations-1.svg]({{ site.github.url }}/res/2023-04-13-Backtracking/permutations-1.svg#middle)

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        dfs( nums, 0 );
        return ans;
    }
    
    void dfs(vector<int>& nums, int depth) {
        if ( depth == nums.size() - 1 ) {
            // Valid answer is found at leaf nodes
            ans.push_back( nums );
            return;
        }
        for ( int i=depth; i<nums.size(); i++ ) {
            // Swap
            swap( nums[i], nums[depth] );
            // Fix nums[0:depth+1], find all permutations for nums[depth+1:]
            dfs( nums, depth + 1 );
            // Backtracking at each iteration
            swap( nums[i], nums[depth] );
        }
    }
    
    vector<vector<int>> ans;
};
```

Approach #2:

Backtracking is done at the end after all iterations.

![permutations-2.svg]({{ site.github.url }}/res/2023-04-13-Backtracking/permutations-2.svg#middle)

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        dfs2( nums, 0 );
        return ans;
    }

    void dfs2(vector<int>& nums, int depth) {
        if ( depth == nums.size() - 1 ) {
            // Valid answer is found at leaf nodes
            ans.push_back( nums );
            return;
        }
        for ( int i=depth; i<nums.size(); i++ ) {
            // Swap
            swap( nums[i], nums[depth] );
            // Fix nums[0:depth+1], find all permutations for nums[depth+1:]
            dfs2( nums, depth + 1 );
        }
        // Backtracking at the end, after all iterations
        for ( int i=nums.size()-1; i>=depth; i-- ) {
            swap( nums[i], nums[depth] );
        }
    }
    
    vector<vector<int>> ans;
};
```

### [Leetcode 47. Permutations II](https://leetcode.com/problems/permutations-ii/description/)

To avoid duplicates, sort the array. In order to maintain the sub-array(`nums[depth]`) as sorted, use the second approach, i.e. do the backtracking at the end after all iterations, and check if `nums[i] != nums[depth]`.

![permutations-ii.svg]({{ site.github.url }}/res/2023-04-13-Backtracking/permutations-ii.svg#middle)

```cpp
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        sort( nums.begin(), nums.end() );
        dfs( nums, 0 );
        return ans;
    }
    
    void dfs(vector<int>& nums, int depth) {
        if ( depth == nums.size()-1 ) {
            // Valid answer is found at leaf nodes
            ans.push_back( nums );
            return;
        }
        for ( int i=depth; i<nums.size(); i++ ) {
            // Assuming `nums` is sorted at this point, as long as `dfs()` doesn't change 
            // `nums`, after each iteration array `nums[depth+1:]` remains sorted.
            // For example, assume nums is {1,2,3,4}  depth is 0:
            // swap 1 & 1 -> {1,2,3,4} -> {2,3,4} is sorted
            // swap 1 & 2 -> {2,1,3,4} -> {1,3,4} is sorted
            // swap 2 & 3 -> {3,1,2,4} -> {1,2,4} is sorted
            // swap 3 & 4 -> {4,1,2,3} -> {1,2,3} is sorted
            if ( i == depth || nums[i] != nums[depth] ) {
                // Swap
                swap( nums[i], nums[depth] );
                // Fix nums[0:depth+1], find all permutations for nums[depth+1:]
                dfs( nums, depth + 1 );
            }
        }
        // Backtracking at the end, after all iterations
        // For example, assume nums is {4,1,2,3}  depth is 0:
        // swap 3 & 4 -> {3,1,2,4}
        // swap 2 & 3 -> {2,1,3,4}
        // swap 1 & 2 -> {1,2,3,4}
        // swap 1 & 1 -> {1,2,3,4}
        for ( int i=nums.size()-1; i>=depth; i--) {
            swap( nums[i], nums[depth] );
        }
    }
    
    vector<vector<int>> ans;
};
```