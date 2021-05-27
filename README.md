### LeetCode001 两数之和
```
给定一个整数数组nums和一个整数目标值target，在该数组中找出和为目标值的那两个整数，
并返回它们的数组下标。假设每种输入只会对应一个答案。但数组中同一个元素在答案里不能
重复出现，可以按任意顺序返回答案。
```
```
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
```
测试可以通过，但是速度比较慢时间复杂度O(n2)。
```
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
```
典型的空间换时间的方式，在哈希表冲突较少的情况下时间复杂度接近O(n)，空间复杂度O(n)。
总结：简单题，但是算法题还是做得少了，虽然学过hash表但是最简单的空间换时间都不会，
现在开始应记牢。


### LeetCode002 两数相加
```
两个非空的链表，表示两个非负的整数。它们每位数字都是按照逆序的方式存储的，并且每个
节点只能存储一位数字。将两个数相加，并以相同形式返回一个表示和的链表，可以假设除了
数字0之外，这两个数都不会以0开头。
```
```
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
```
这里有个关于leetcode的小细节，新建节点不能malloc必须new，否则运行出错。
典型的暴力法，时间复杂度为O(n)，空间复杂度为O(n)，此处两个n都是最长的链表长度。
结果的时间复杂度还行，但是空间复杂度过高了
```
解题思路1.1：在遍历的过程中就生成结果链表，避免临时数组的使用，也少写一个for循环。
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        int parity=0;
        ListNode *iterator1 = l1;
        ListNode *iterator2 = l2;
        int value = 0;
        int i = 0;
        int endloop = 0;
        ListNode *ret = new ListNode;
        ListNode *prev = new ListNode;
        while(endloop!=1){
            ListNode *current = new ListNode;
            current->next = NULL;
            if(iterator1 != nullptr && iterator2 != nullptr){
                value=(parity + iterator1->val + iterator2->val)%10;
                if(parity + iterator1->val + iterator2->val > 9){
                    parity = 1;
                }
                else{
                    parity = 0;
                }
                i++;
                current->val = value;
                iterator1 = iterator1->next;
                iterator2 = iterator2->next;
            }
            else if(iterator1 != nullptr && iterator2 == nullptr){
                value=(parity + iterator1->val)%10;
                if(parity + iterator1->val > 9){
                    parity = 1;
                }
                else{
                    parity = 0;
                }
                i++;
                current->val = value;
                iterator1 = iterator1->next;
            }
            else if(iterator1== nullptr && iterator2 != nullptr){
                value = (parity + iterator2->val)%10;
                if(parity + iterator2->val > 9){
                    parity = 1;
                }
                else{
                    parity = 0;
                }
                i++;
                current->val = value;
                iterator2 = iterator2->next;
            }
            else if(iterator1== nullptr && iterator2 == nullptr){
                value=parity;
                if(parity){
                    i++;
                    current->val = value;
                    current->next = NULL;
                }
                endloop = 1;
            }
            if(endloop!=1||(endloop==1&&parity==1)){
                if(i==1){
                    ret = current;
                    prev = current;
                }
                else{
                    prev->next = current;
                    prev = current;
                }
            }
        }
        return ret;

    }
};
```
这是很容易想到的写法，但是代码搓的少，就先写了个简答版在改进。最终的速度还行，
但是内存只超过了%8的人，看了一下题解，有人用输入链表作返回，个人认为有点本末
倒置，我的算法空间复杂度O(n)都是用来新建返回链表了，感觉不需要改进了。

### LeetCode003 无重复最长子串
```
给定一个字符串，找出其中不含有重复字符的最长子串的长度。
```
```
解题思路1：暴力破解三层for循环，遍历每个字符作为开头的不重复子串长度，但是时间
复杂度O(n2)还要借助哈稀图来判断是否有重复，很笨重，显然有更简单的方。
```
```
解题思路2：使用滑动窗口法，可以看出，实际上选择好左边的起始字符之后，用哈稀图记
录下遍历过得中间字符，每次遭遇重复时由于已知和几号字符重复，所以可以将左边的起始
字符向右移动到重复的位置上，然后接着上次的进度继续遍历。使用左右指针和一个哈稀图
来做（也可以用int[128]来做，因为ASCII码只有128个）。
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        map<char, int> array_map;
        int tmpmax = 0;
        int windowsize = 0;
        map<char,int>::iterator iter;
        int left = 0;
        for(int i=0;i<s.size();i++){//i是又指针left是左指针
            iter = array_map.find(s[i]);
            if(iter == array_map.end()){//如果哈西图中无重复元素则插入
                array_map.insert(pair<int, int>(s[i],i ));
                windowsize++;
            }
            else{
                if(iter->second>=left){//如果有重复，则判断是否在窗口间
                    tmpmax = max(tmpmax,windowsize);
                    left = iter->second+1;//重复的话，left左移一位
                    windowsize = i-left+1;
                    array_map.erase(iter);//在窗口间则删原记录插新记录
                    array_map.insert(pair<int, int>(s[i],i ));
                }
                else{         
                    windowsize++;//如果不在窗口间则增大窗口，覆盖原重复元素
                    array_map.erase(iter);
                    array_map.insert(pair<int, int>(s[i],i ));
                }
            }
        }
        tmpmax = max(tmpmax,windowsize);//防止最后一次最大
        return tmpmax;
    }
};

```
看答案之前想了很久，总觉得自己能想到，看了答案才发现思考的不够，滑动窗口需要多练，
中间那里如何判断是否在窗口内自己没思考出来，真是蠢死。时间复杂度O(n)空间复杂度也
时O(n)。

### LeetCode004 两正序数组求中位数
```
给定两个有序数组，其中起码一个不为空，求他们两个数组集合中的整体中位数。偶数位则
返回中间两个数的均值，奇数则返回中间数。
```
```
解题思路1：使用一个临时数组储存，ij两个指针循环比较两个数组中的元素，将小的数放入
临时数组，小的数组下表++，若出现某个数组访问到最后并且小于另一个数组的当前元素，
则将所有剩余元素导入临时数组，最后根据下标取中位数。

```
```
解题思路2：实际上不需要临时数组，只需要比较固定次数，每次记录下比较完毕的较小的数。

对于奇数个数字，比较len/2+1次，对于偶数，由于需要两个数，所以比较次数也是len/2+1，
这样一来循环的次数就确定了，分别用两个指针做对比，此时，判断并且去较小的数的条件有
两个，1：i未越界:2：i指向的数组1中的元素小于j指向的数组2的元素或者j已经越界。这样
可以避免处理过多的边界问题。
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int leng1 = nums1.size();
        int leng2 = nums2.size();
        int length = leng2 + leng1;
        int start1=0;
        int start2=0;
        double left = 0;
        double right = 0;
        for(int i=0;i<=length/2;i++){
            left = right;//这个循环条件在start2超限时不会越界访问数组。
            if(start1<leng1&&(start2>=leng2||nums1[start1]<nums2[start2])){
                right = nums1[start1++];
            }
            else{
                right = nums2[start2++];
            }
        }
    if (length%2 == 0)
        return (left + right) / 2.0;
    else
        return right;
    }
};
```
这一题也思考的很久但是也没写出来，问题主要在于死磕了边界问题，想用标志位分是否越界，
但是又可能导致边界上的元素无法进入判断大小，最后把逻辑改的面目全非思路也混乱实际上
判断条件中从左到右会依次如果越界则已经满足了if，不会去访问越界的数组地址。

### LeetCode005 最长回文子串
```
形如aba或者abba叫做回文子串，即从左到右从右到左都是同样的字符串，现给定一个字符串，
串长大于一，求他的最长回文子串。
```
```
解题思路：中心扩展法，回文串最短为1，如果在此基础上扩展，则需要中间元素的左右都相同，
如果符合回文则在左右继续扩展，对于bb类型同样左右扩展，所以进行两个for循环，谁更长，
得到结果之后根据循环中的index返回子串。
class Solution {
public:
    string longestPalindrome(string s) {
        int maxindex=0,maxlength=1,emaxindex=0,emaxlength=0;
        int j=0;
        for(int i=0;i<s.length();i++){//寻找以单个字符为中心的对称子串
            j=1;//这里先判断是否越界再决定是否访问
            while((i-j>=0&&i+j<s.length())&&(s[i-j]==s[i+j])){
                if(2*j+1>=maxlength){
                    maxlength = 2*j+1;
                    maxindex = i;
                }
                j++;
            }
        }
        for(int i=0;i<s.length();i++){//寻找以两个字符为中心的对称子串
            j=1;
            if(i+1<s.length()&&s[i]==s[i+1]){
                if(emaxlength==0){
                    emaxlength = 2;
                    emaxindex=i;
                }//这里先判断是否越界再决定是否访问
                while((i-j>=0&&i+j<s.length())&&(s[i-j]==s[i+j+1])){
                    if(2*j+2>=emaxlength){
                        emaxlength = 2*j+2;
                        emaxindex = i;
                    }
                    j++;
                }
            }            
        }
        if(emaxlength>maxlength){//判断哪个串长
            return  s.substr(emaxindex-(emaxlength)/2+1,emaxlength);
        }
        else{
            return  s.substr(maxindex-(maxlength-1)/2,maxlength);
        }
    }
};
```
测试可以通过，速度超过了90多的答案，变量个数为常数，空间复杂度也没问题。


