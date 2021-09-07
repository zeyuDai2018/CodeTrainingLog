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

### LeetCode198 动态规划-打家劫舍1
```
给定一个非负的数组X，每个代表一个存有现金X[i]的房间。取走一个数字代表
盗取这个房间的钱。如果两间相邻的房屋被盗，系统会自动报警。计算不触动警
报装置的情况下能够偷窃到的最高金额。
```
```
解题思路：先思考，如果在数组长度为1的情况下，则必定盗取，两家的话，则盗
取第一家或者第二家。现由2家向后扩展盗n家，偷窃第n间房屋，那么就不能偷窃
第n-1间房屋，偷窃总金额为前n−2间房屋的最高总金额与第n间房屋的金额之和。
不偷窃第n间房屋，偷窃总金额为前n-1间房屋的最高总金额。这两个比大小，则
得到n间房子的最大值，因此其实扩大到n家时，我们需要记录有n-1家时的最大值和
n-2家时的最大值，因此代码如下：

class Solution {
public:
    int rob(vector<int>& nums) {
        //vector<int> gain(cost.size()+1,0);
        int pprev = 0;
        int prev = 0;
        int cur = 0;
        for(int i=0; i<nums.size(); i++){
           cur = max(pprev+nums[i],prev);
           pprev = prev;
           prev =cur;
        }
        return cur;
    }
};
```
测试可以通过，速度0ms，变量个数为常数所以空间复杂度也没问题。

### LeetCode213 动态规划-打家劫舍2
```
给定一个非负的数组X，每个代表一个存有现金X[i]的房间。取走一个数字代表
盗取这个房间的钱。如果两间相邻的房屋被盗，系统会自动报警，其中房间成环
形即最后一家与第一家为邻居，同时窃取将报警。计算不触动警报装置的情况下
能够偷窃到的最高金额。
```
```
解题思路：同样是动态规划，但是此时由于是环形，所以我们拆成2种情况，第一
种偷取第一家，那么题目称为打家劫舍1，但是输入变为第二家到倒数第二家，第
二种是不偷第一家，这样题目变为打家劫舍1，输入变成第二家到最后一家。这里
取二者最大值即为该题目最优解。
此代码如下：

class Solution {
public:
    int rob_range(vector<int>& nums, int start, int end) {
        int pprev = 0;
        int prev = 0;
        int cur = 0;
        for(int i=start; i<end+1; i++){
           cur = max(pprev+nums[i],prev);
           pprev = prev;
           prev =cur;
        }
        return cur;
    }
    int rob(vector<int>& nums) {
        int first_rob = nums[0];
        int first_notrob = nums[nums.size()-1];
        first_rob = first_rob + rob_range(nums,2,nums.size()-2);
        first_notrob = rob_range(nums,1,nums.size()-1);
        return max(first_rob,first_notrob);
    }
};
```
测试可以通过，速度0ms，变量个数为常数所以空间复杂度也没问题。

### LeetCode740 动态规划-删除并取点
```
给你一个整数数组nums，你可任意删除其中一个数并且获取它的点数，但是每次操
作中，取走任意一个nums[i]之后，数组中所有等于nums[i]-1和nums[i]+1的元素
都会消失，你可以多次选取直到数组为空。返回能通过这些操作获得的最大点数。
```
```
解题思路：同样是动态规划，其实也是打家劫舍，这题相当于说是对于钱数为n的房
可能有很多家，而且一旦钱数为n的房子失窃，钱数为n+1和n-1的房子就不能在再被
偷窃（会警报），相当于打家劫舍中的相邻房屋不能一起失窃的条件。因此本题可以
转化为打家劫舍，首先是排序，由于排序后若出现非相邻数值，那么他们的决策将不
会影响到对方，之后遍历整个数组，所以将整个数组分成多个数组值相邻的段，每一
段分别使用打家劫舍1的算法，此时每个数字的gain不同，应该为每个数字乘以他出
现的次数。每段都打劫完之后最后求和则得到最大值。
此代码如下：

class Solution {
public:
    int rob_range(vector<int>& nums, int start, int end) {
        int pprev = 0;
        int prev = 0;
        int cur = 0;
        for(int i=start; i<end+1; i++){
           cur = max(pprev+nums[i],prev);
           pprev = prev;
           prev =cur;
        }
        return cur;
    }
    int deleteAndEarn(vector<int>& nums) {
        vector<int> buf(nums.size()+1,0);
        vector<int> gain(nums.size()+1,0);
        vector<int> index(nums.size()+1,0);
        sort(nums.begin(), nums.end());
        int prev = nums[0],samecount = 0,roblength = 1;
        int index_ct = 0,gain_total = 0,groups = 0;
        for(int i=0;i<nums.size();i++){
            if(prev == nums[i]){// if equals last number 
                samecount++;
                gain[index_ct] = nums[i]*samecount;
                if(i==nums.size()-1){index[++groups] = roblength;}
            }
            else if (prev == nums[i]-1){// if a number that is 1 bigger
                samecount = 1;
                gain[++index_ct] = nums[i]*samecount;
                prev = nums[i];
                roblength++;
                if(i==nums.size()-1){index[++groups] = roblength;}
            }
            else{//if the number is gapped
                samecount = 1;
                gain[++index_ct] = nums[i]*samecount;
                if(i==nums.size()-1){index[groups+1] = 1;}
                index[groups++] = roblength;
                roblength = 1;
                prev = nums[i];
            }
        }
        int ct = 0;
        for(int i=0;i<=groups;i++){
            gain_total = gain_total + rob_range(gain,ct,ct+index[i]-1);
            ct = ct + index[i];
        }
        return gain_total;
    }
};
```
测试可以通过，时间复杂度nlog(N),速度8ms，空间复杂度为O(3N)。

### LeetCode055 动态规划-贪心算法-跳跃游戏1
```
给定一个非负整数数组nums，最初位于数组的第一个下标。数组中的每个元素代表
你在该位置可以跳跃的最大长度。判断是否能够到达最后一个下标。
```
```
解题思路1：动态规划，由于此时要到达最后一个则必须在最后一个元素前面有可以
到达最后一个元素的跳板元素，依次类推，每个元素都需要一个跳板元素，因此可以
从前向后搜索，搜到第一个可以跳到最后元素的元素时，记录下来，下一次继续搜索
但是此时的数组变为0号元素到上一次搜索到的元素，循环执行，直到搜索返回零号
元素或者目标不可达。代码如下：

class Solution {
public:
    int find_mid(vector<int>& nums,int end){
        int x = -1;
            for(int i=0;i<end;i++){
                if(end-i<=nums[i]){
                    x = i;
                }
            }
            return x;

    }
    bool canJump(vector<int>& nums) {
        int tmp = nums.size()-1;
        while(tmp!=0){
            tmp = find_mid(nums,tmp);
            if(tmp==-1){return 0;}
        }
        return 1;
    }
};
```
测试可以通过，速度1848ms，时间复杂度O(n2),变量个数有限，因此为空间
复杂度是1。
```
解题思路2：贪心算法，由于此时我们每次在知道当前能跳多远之后，则得到一个区间
，在这个区间中，我们可以任意选择跳板且跳的最大落点我们也知道，跳的越远越好，
所以我们直接做一次循环，维护tmpmax变量，每次在下标小于tmpmax的元素中计算他
的最大跳跃落点，若大于tmpmax则更新，直到tmpmax不小于数组长度，或者搜索到
tmpmax但是无法到达数组终点则失败。代码如下：

class Solution {
public:
    bool canJump(vector<int>& nums) {
        int tmp = 0;
        for(int i=0;i<nums.size();i++){
            if(i<=tmp){
                tmp = max(tmp,i+nums[i]);
            }
        }
        if(tmp>=nums.size()-1){return 1;}
        return 0;
    }
};
测试可以通过，速度44ms，时间复杂度O(n),变量个数有限，因此为空间
复杂度是1。
```

### LeetCode045 动态规划-贪心算法-跳跃游戏2
```
给定一个非负整数数组nums，最初位于数组的第一个下标。数组中的每个元素代表
你在该位置可以跳跃的最大长度。判断最少需要多少步才能跳到最后一格。假设数组
稳定可以跳到最后。
```
```
解题思路1：动态规划，按照跳跃游戏1的思路动态规划，要跳最后一格必须跳到能到
最后一格的下标最小的一格，直接在循环中记录运行次数即可代码如下：

class Solution {
public:
    int find_mid(vector<int>& nums,int end){
        int x = -1;
            for(int i=0;i<end;i++){
                if(end-i<=nums[i]){
                    x = i;
                    return x;//一旦找到可达的，直接返回作为下一次的end
                }
            }
            return x;
    }
    int jump(vector<int>& nums) {
        int tmp = nums.size()-1;
        int count = 0;
        while(tmp!=0){
            tmp = find_mid(nums,tmp);
            count++;
        }
        return count;
    }
};
```
测试可以通过，速度80ms，时间复杂度O(n2),变量个数有限，因此为空间
复杂度是1。
```
解题思路2：贪心算法，由于每次在知道当前能跳多远之后，会得到一个区间，在这个
区间中，我们可以任意选择跳板且跳的最大落点我们也知道，跳的越远越好，所以我们
直接做一次循环，维护tmpmax变量，每次在下标小于tmpmax的元素中找到他的最大跳
跃落点和起跳的元素，依次循环，每次都找到最大跳跃落点和跳板序号，直到tmpmax
不小于数组长度，代码如下：

class Solution {
public:
    int jump(vector<int>& nums) {
        int n = nums.size()-1,count=0;
        int i=0,tmpmax=0,index=0;
        while(nums[i]+i<n){
            for(int j=i+1;j<=i+nums[i];j++){//在区间内找跳最远的跳板
                if(j+nums[j]>=tmpmax){
                    tmpmax = j+nums[j];
                    index = j;
                }
            }
            i = index;//改变搜索起点
            count++;
        }
        if (n==0) return count;
        return count+1;
    }
};
测试可以通过，速度18ms，时间复杂度O(n2),变量个数有限，因此为空间
复杂度是1。
```
```
解题思路3：贪心算法，改进上一个算法，由于在231454这种情况下会出现多次查询
中间那个1是否能作为最大跳板的情况（2做起点查询一次，3作起点查询一次），
所以为了避免这种情况，我们让i只增不减，只在跳完之后再更新max，每次跳完计数
加一，代码如下：

class Solution {
public:
int jump(vector<int>& nums)
    {
        int ans = 0;
        int end = 0;
        int maxPos = 0;
        for (int i = 0; i < nums.size() - 1; i++)
        {
            maxPos = max(nums[i] + i, maxPos);
            if (i == end)
            {
                end = maxPos;
                ans++;
            }
        }
        return ans;
    }

};
测试可以通过，速度8ms，时间复杂度O(n),变量个数有限，因此为空间
复杂度是1。
```

### LeetCode055 动态规划-贪心算法-跳跃游戏1
```
给定一个非负整数数组nums，最初位于数组的第一个下标。数组中的每个元素代表
你在该位置可以跳跃的最大长度。判断是否能够到达最后一个下标。
```
```
解题思路1：动态规划，由于此时要到达最后一个则必须在最后一个元素前面有可以
到达最后一个元素的跳板元素，依次类推，每个元素都需要一个跳板元素，因此可以
从前向后搜索，搜到第一个可以跳到最后元素的元素时，记录下来，下一次继续搜索
但是此时的数组变为0号元素到上一次搜索到的元素，循环执行，直到搜索返回零号
元素或者目标不可达。代码如下：

class Solution {
public:
    int find_mid(vector<int>& nums,int end){
        int x = -1;
            for(int i=0;i<end;i++){
                if(end-i<=nums[i]){
                    x = i;
                }
            }
            return x;

    }
    bool canJump(vector<int>& nums) {
        int tmp = nums.size()-1;
        while(tmp!=0){
            tmp = find_mid(nums,tmp);
            if(tmp==-1){return 0;}
        }
        return 1;
    }
};
```
测试可以通过，速度1848ms，时间复杂度O(n2),变量个数有限，因此为空间
复杂度是1。
```
解题思路2：贪心算法，由于此时我们每次在知道当前能跳多远之后，则得到一个区间
，在这个区间中，我们可以任意选择跳板且跳的最大落点我们也知道，跳的越远越好，
所以我们直接做一次循环，维护tmpmax变量，每次在下标小于tmpmax的元素中计算他
的最大跳跃落点，若大于tmpmax则更新，直到tmpmax不小于数组长度，或者搜索到
tmpmax但是无法到达数组终点则失败。代码如下：

class Solution {
public:
    bool canJump(vector<int>& nums) {
        int tmp = 0;
        for(int i=0;i<nums.size();i++){
            if(i<=tmp){
                tmp = max(tmp,i+nums[i]);
            }
        }
        if(tmp>=nums.size()-1){return 1;}
        return 0;
    }
};
测试可以通过，速度44ms，时间复杂度O(n),变量个数有限，因此为空间
复杂度是1。
```

### LeetCode053 动态规划-最大子序和
```
给定一个整数数组nums，求他最大的连续子序列和1,2，-3,4则最大子序为4。
```
```
解题思路：动态规划，若我们已知以a[i]为结尾的数组最大子序和，那么a[i+1]的最
大子序和则是max(a[i+1],a[i+1]+maxsum(a[i]))，因为由a[0]为结尾的数组最大子
序和就是a[0]，因此可以用一个for循环搞定。是典型的动态规划题。
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int maxsum=nums[0];
        int movingsum=nums[0];
        for (int i=1;i<nums.size();i++){
            movingsum = max(nums[i],movingsum+nums[i]);
            maxsum = max(maxsum,movingsum);
        }
        return maxsum;
    }
};

测试可以通过，速度4ms，时间复杂度O(n),变量个数有限，因此为空间复杂度是1。
```

### LeetCode918 动态规划-环形最大子序和
```
给定一个整数数组nums，这里数组为环形即最后一个数的下一个数为第一个数，求他最大
的连续子序列和例如1,2，-3,4则最大子序不是4而是4+1+2=7。
```
```
解题思路：动态规划，因为已经知道如何解决非环形情况，因此我们可以直接拆成两种情况，
第一种，最大子序中不包含a[size-1]和a[0]，那么相当于无环就是正常最大子序和。第二
种，最大子序包含了a[size-1]和a[0]时，那么由于整个数组和固定，那么其实是要在数组
删去两头之后求剩下数组的最小子序和并且删掉，剩下的就是原数组的最大环形子序和。两
者中最大的就是最大值。
class Solution {
public:
    int maxSubarraySumCircular(vector<int>& nums) {
        int max_=nums[0];
        int maxsum=nums[0];
        int movingsum=nums[0];
        int minsum=nums[0];
        if(nums.size()==1)return nums[0];
        for (int i=1;i<nums.size();i++){
            movingsum = max(nums[i],movingsum+nums[i]);
            maxsum = max(maxsum,movingsum);
        }
        minsum= nums[1];
        movingsum = nums[1];
        for (int i=2;i<nums.size()-1;i++){
            movingsum = min(nums[i],movingsum+nums[i]);
            minsum = min(minsum,movingsum);
        }
        int tmp = 0;
        for (int i=0;i<nums.size();i++){
            tmp = nums[i]+tmp;
        }
        max_ = max(tmp-minsum,maxsum);
        return max_;
    }
};
测试可以通过，速度64ms，时间复杂度O(n),变量个数有限，因此为空间复杂度是1。
```

### LeetCode152 动态规划-乘积最大子数组
```
给定一个整数数组nums，求他最大的连续子序列乘积例如1,2，-3,4则最大子序积是4。
```
```
解题思路：动态规划，和最大子序和类似，由于此时最大值可能会在正负值之间跳变，
所以此时应该维护两个变量，最小乘积最大乘积，的最大乘积在以下三个数字中，
num[i],num[i]*product_min，num[i]*product_max，最小值也在以下三个数字中，
每次更新都同时更新product_min和product_max以及最后要返回的max。

class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int mulmax1 = nums[0];
        int mulmax2 = nums[0];
        int mulmin1 = nums[0];
        int mulmin2 = nums[0];
        int tpmax = nums[0];
        int tpmin = nums[0];
        for(int i=1;i<nums.size();i++){
            mulmax1 = max(mulmax2*nums[i],mulmin2*nums[i]);
            mulmin1 = min(mulmin2*nums[i],mulmax2*nums[i]);
            mulmax2 = max(nums[i],mulmax1);
            tpmax = max(tpmax,mulmax2);
            mulmin2 = min(mulmin1,nums[i]);
            //cout<<"the maxpto is "<< mulmax<<endl;
            //cout<<"the minpto is "<< mulmin<<endl;
            tpmin = min(tpmin,mulmin2);
        }
        return tpmax;
    }
};
测试可以通过，速度8ms，时间复杂度O(n),变量个数有限，因此为空间复杂度是1。
```

### LeetCode1567 动态规划-乘积正数最大子数组长
```
给定一个整数数组nums，求他乘积为正数的最长子数组长度。
```
```
解题思路：动态规划，和最大子序积类似，由于现在有正负交替，因此肯定也是需要维
护2个变量，假设我们现在知道pos_len_max(num[i])和neg_len_max(num[i])，那
么我们对于i=i+1时的这两个变量值是可以根据当前的num[i]推测出来的，具体逻辑看
代码。总结一下就是关于数组问题，动态规划时可以设置一个dp(nums[i]),然后随着
i增写出i=i+1时dp值的表达式，但是很多题这样做不出来，比如现在的乘法会出现跳变
所以需要维护两个变量neg和pos来做。

class Solution {
public:
    int getMaxLen(vector<int>& nums) {
        int neglen_pre = 0;
        int poslen_pre = 0;
        int neglen = 0;
        int poslen = 0;
        int swap = 0;
        int max_poslen = 0;
        int mulres = nums[0];
        for(int i=0;i<nums.size();i++){
            if(nums[i]<0){
                if(neglen_pre!=0){                
                    poslen = neglen_pre+1;
                }
                else{
                    poslen = 0;
                }
                if(poslen_pre!=0){
                    neglen = poslen_pre+1;
                }
                else{
                    neglen = 1;
                }
            }
            else if(nums[i]>0){
                if(poslen_pre!=0){                
                    poslen = poslen_pre+1;
                }
                else{
                    poslen = 1;
                }
                if(neglen_pre!=0){                
                    neglen = neglen_pre+1;
                }
                else{
                    neglen = 0;
                }
            }
            else{
                poslen=0;
                neglen=0;
            }
            max_poslen = max(max_poslen,poslen);
            poslen_pre = poslen;
            neglen_pre = neglen;
        }
        return max_poslen;
    }
};
测试可以通过，速度88ms，时间复杂度O(n),变量个数有限，因此为空间复杂度是1。
```


### LeetCode121 买卖股票的最佳时机
```
给定一个整数数组prices，其中每个数字为当天的股票销售价格，只能买卖一次，求最
大能获得的利益。
```
```
解题思路：动态规划，首先思考一次遍历数组，下标从0到len-1，那么假设我们知道
当前从左到右长度为i+1的数组中的最小购入价格即min_和最大收益max_gain,那么
i+2长度数组的最大收益则应当为当前price[i+1]-min_和上一轮max_gain的最大值。
同时，此时应该更新当前数组的最小购入价格即min_。由此我们得到了这一递推公式，
使用c语言实现如下：

class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int gain = 0;
        int min_ = prices[0];
        for (int i=0;i<prices.size();i++){
            min_ = min(min_,prices[i]);
            gain = max(gain,prices[i]-min_);
        }
        return gain;
    }
};
测试可以通过，速度100ms，时间复杂度O(n),变量个数有限，因此为空间复杂度是1。
```

### LeetCode122 买卖股票的最佳时机2
```
给定一个整数数组nums，其中每个数字为当天的股票销售价格，可以任意次买卖股票，
但是同时只能持有一只股票，即只能买卖买卖，不能买买卖。求在这些天数内最大能
获得的利益。
```
```
解题思路1：动态规划，首先思考，假设有两个变量分别表示前一天持股所得最大收益
P_Stock和当天不持股所得最大收益P_nstock，要计算明天的持股最大收益C_Stock和
不持股C_nstock最大收益，那么其实可以推出，若此时当天价格为price[i],那么此时
C_stock只能由P_stock保持不变或P_nstock并且当天购买得到，因此C_stock为二两种
情况获益的最大值，而C_nstock则是从P_nstock当天不购买和P_stock当天卖出的收益
两者最大值。易得代码如下：

class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int C_stock = 0;
        int C_nstock = 0;
        int P_stock = -prices[0];
        int P_nstock = 0;
        int gain_total = 0;
        int gain_max = 0;
        int len_ = prices.size();
        for(int i=0;i<len_;i++){
            C_nstock = max(P_stock + prices[i],P_nstock);
            C_stock = max(P_nstock - prices[i],P_stock);
            P_stock = C_stock;
            P_nstock = C_nstock;
        }
        return C_nstock;
    }
};
测试可以通过，速度4ms，时间复杂度O(n),变量个数有限，因此为空间复杂度是1。
```

```
解题思路2：贪心算法，由于完全不限制购买次数，因此我们可以设想，只要我发现后
一天的出售价格比前一天的购买价格高，那额前一天买后一天卖就是有意义的，由此，
只要遍历一次数组，每次遍历到prices[i]时记录i-1的价格，若i-1低于i价格，那么
就加入总收益。

class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.size()==1) return 0;
        int leng_=prices.size();
        int gain = 0;
        for(int i=0;i<leng_;i++){
            if(i!=leng_-1){
                if(prices[i]<prices[i+1]){
                    gain = gain + prices[i+1] - prices[i];
                }
            }
        }
        return gain;
    }
};
测试可以通过，速度4ms，时间复杂度O(n),变量个数有限，因此为空间复杂度是1。
```
