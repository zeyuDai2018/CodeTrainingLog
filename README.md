### LeetCode001
```
题目：
给定一个整数数组nums和一个整数目标值target，在该数组中找出和为目标值的那两个整数，
并返回它们的数组下标。假设每种输入只会对应一个答案。但数组中同一个元素在答案里不能
重复出现，可以按任意顺序返回答案。

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

总结：简单题，但是算法题还是做得少了，虽然学过hash表但是最简单的空间换时间都不会，
现在开始应记牢。
```

### LeetCode002
```
两个非空的链表，表示两个非负的整数。它们每位数字都是按照逆序的方式存储的，并且每个
节点只能存储一位数字。将两个数相加，并以相同形式返回一个表示和的链表，可以假设除了
数字0之外，这两个数都不会以0开头。

/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
 
解题思路1：用两个指针遍历两个链表，低位对应相加，用数组记录结果同时记下进位用于高
位相加。链表相加结束之后，重新建立一个链表，将数组中的值加入链表中。
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        int parity=0;
        ListNode *iterator1 = l1;
        ListNode *iterator2 = l2;
        vector<int> values(100,0);
        int i = 0;
        while(1){
            if(iterator1 != nullptr && iterator2 != nullptr){
                values[i]=(parity + iterator1->val + iterator2->val)%10;
                if(parity + iterator1->val + iterator2->val > 9){
                    parity = 1;
                }
                else{
                    parity = 0;
                }
                i++;
                iterator1 = iterator1->next;
                iterator2 = iterator2->next;
            }
            else if(iterator1 != nullptr && iterator2 == nullptr){
                values[i]=(parity + iterator1->val)%10;
                if(parity + iterator1->val > 9){
                    parity = 1;
                }
                else{
                    parity = 0;
                }
                i++;
                iterator1 = iterator1->next;
            }
            else if(iterator1== nullptr && iterator2 != nullptr){
                values[i]=(parity + iterator2->val)%10;
                if(parity + iterator2->val > 9){
                    parity = 1;
                }
                else{
                    parity = 0;
                }
                i++;
                iterator2 = iterator2->next;
            }
            else if(iterator1== nullptr && iterator2 == nullptr){
                values[i]=parity;
                if(parity)
                i++;
                break;
            }
        }
        ListNode *tmp = new ListNode;
        for (int j=i-1; j>=0;j--){
            ListNode *p = new ListNode;
            p->val = values[j];
            if(j==i-1){
                p->next = NULL;
                tmp = p;
            }
            else{
                p->next = tmp;
                tmp = p;
            }
        }
        return tmp;

    }
};
这里有个关于leetcode的小细节，新建节点不能malloc必须new，否则运行出错。
典型的暴力法，时间复杂度为O(n)，空间复杂度为O(n)，此处两个n都是最长的链表长度。
结果的时间复杂度还行，但是空间复杂度过高了，需要改进的是在遍历的过程中就实现结
果链表的生成，从而避免临时数组的使用。

```
