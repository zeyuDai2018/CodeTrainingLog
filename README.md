### LeetCode001
```
题目：
给定一个整数数组nums和一个整数目标值target，在该数组中找出和为目标值的那两个整数，并返回它们的数组下标。
假设每种输入只会对应一个答案。但数组中同一个元素在答案里不能重复出现，可以按任意顺序返回答案。

解题思路1：暴力破解两层for代码如下
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
测试可以通过，但是速度比较慢时间复杂度O(n2)。
解题思路2：看了题解，用哈希图记录已经查询过得元素，这样以后的查询效率就是O(1)
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {     
        vector<int> twovalues(2);
        map<int, int> array_map;
        int tmp = 0;
        map<int,int>::iterator iter;
        for(int i=0;i<nums.size();i++){
            tmp = target - nums[i];
            iter = array_map.find(tmp);
            if(iter!= array_map.end()){
                twovalues[0]=i;
                twovalues[1]=iter->second;
                break;
            }
            else{
                array_map.insert(pair<int, int>(nums[i],i ));
            }
        }
        return twovalues;
    }
};
典型的空间换时间的方式，在哈希表冲突较少的情况下时间复杂度接近O(n)，空间复杂度O(n)。
```
