# Search for a Range

### 描述

Given a sorted array of integers, find the starting and ending position of a given target value.

Your algorithm’s runtime complexity must be in the order of O(log n).
If the target is not found in the array, return [-1, -1].
For example, Given [5, 7, 7, 8, 8, 10] and target value 8, return [3, 4].

```c++
//STL
//O(logn) O(1)
class Solution {
    public:
    vector<int> searchRange(vector<int>& nums, int target) {
        const int l = distance(nums.begin(), lower_bound(nums.begin(),nums.end(),target));
        const int u = distance(nums.begin(), prev(upper_bound(nums.begin(),nums.end(),target)));
        if (nums[l] != target)
            return vector<int> { -1, -1 };
        else
            return vector<int>{ l, u }
    }
}
```

