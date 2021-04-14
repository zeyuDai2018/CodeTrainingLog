### LeetCode001
```
题目：
给定一个整数数组nums和一个整数目标值target，在该数组中找出和为目标值的那两个整数，
并返回它们的数组下标。假设每种输入只会对应一个答案。但数组中同一个元素在答案里不能
重复出现，可以按任意顺序返回答案。

解题思路：暴力破解两层for代码如下

class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {     
        vector<int> twovalues(2);
        for(int i=0;i<nums.size()-1;i++){
            for(int j=i+1;j<nums.size();j++){
                if(nums[i]+nums[j]==target){
                    twovalues[0]=i;
                    twovalues[1]=j;
                }
            }
        }
        return twovalues;
    }
};
测试可以通过，但是速度比较慢时间复杂度O(n2)，看了题解，用哈希图，达到了时间复杂度
O(n)但是空间复杂度变成了O(n)，题解如下

```
