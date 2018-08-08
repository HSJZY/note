# Leetcode 刷题笔记
### 1.两数之和

> * 题目描述：给定一个整数数组和一个目标值，找出数组中和为目标值的两个数。你可以假设每个输入只对应一种答案，且同样的元素不能被重复利用。

> * 基本思路： 建立一个哈希表，依次将输入的数字传入哈希表中，再对每一个数字循环，当前读取到的数当第一个数，在哈希表中查找(target-first_val)，如果存在，即找到这两数之和等于目标值了。

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int> map;
        vector<int> res;
        
        for(int i=0;i<nums.size();i++)
        {
            map.insert(make_pair(nums[i],i));
        }
        for(int i=0;i<nums.size();i++)
        {
            int complement=target-nums[i];
            unordered_map<int, int>::iterator it=map.find(complement);
            if(it!=map.end()&&it->second!=i)
            {
                
                res.push_back(i);
                res.push_back(it->second);
                return res;
            }
        }
        return res;
    }
};
```
------
### ２.两数相加

> * 题目描述：给定两个非空链表来表示两个非负整数。位数按照逆序方式存储，它们的每个节点只存储单个数字。将两数相加返回一个新的链表。
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
> * 解题思路：新建一个链表，带上一个carry位，依次相加，考虑进位的情况就行。

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        int carry=0;
        ListNode* addTwoListNode=new ListNode((l1->val+l2->val)%10),*rear=addTwoListNode;
        
        carry=(l1->val+l2->val)/10;
        l1=l1->next;l2=l2->next;
        while(l1!=NULL||l2!=NULL||carry!=0)
        {
            //calculate val1 and val2,then switch two listNode to its next
            int val1=0,val2=0;
            if(l1==NULL&&l2==NULL){val1=0;val2=0;}
            else if(l1==NULL){val2=l2->val;l2=l2->next;}
            else if(l2==NULL){val1=l1->val;l1=l1->next;}
            else {val1=l1->val;val2=l2->val;l1=l1->next;l2=l2->next;}
            
            //Example 4,6 sumTwoVal=10,nodeVal=0,carry=1
            int sumTwoVal,nodeVal;
            sumTwoVal=val1+val2;
            nodeVal=(sumTwoVal+carry)%10;
            carry=(sumTwoVal+carry)/10;
            
            ListNode* p=new ListNode(nodeVal);
            p->next=rear->next;
            rear->next=p;
            rear=p;
            
        }
        return addTwoListNode;
    }
};
```
------
### ３.无重复字符的最长子串

> * 题目描述：给定一个字符串，找出不含有重复字符的最长子串的长度。
示例：
给定 "abcabcbb" ，没有重复字符的最长子串是 "abc" ，那么长度就是3。
给定 "bbbbb" ，最长的子串就是 "b" ，长度是1。
给定 "pwwkew" ，最长子串是 "wke" ，长度是3。请注意答案必须是一个子串，"pwke" 是 子序列  而不是子串。
> * 解题思路：
(1)可行但效率不高O(n^2)：双指针，一个指头，一个指尾，每次都新建一个哈希表，碰到重复的停下，如果这个子串长度大于之前最大，记录下来，头指针前移到之前重复出现字符地方，新建哈希表，从这个地方开始继续循环，直到结束。
(2)我觉得吧，上面的既然是滑动窗口，那么我们其实只需要在哈希表中记录上一个出现该字符的位置，当我的首指针的位置大于上一个出现该字符的位置的时候，不需要进行考虑，更新当前哈希表中当前数字的位置，继续向后走就可以的。(我觉得这个方式是最好的)
(3)使用string 中的find 函数，如果找到前边有出现过，新建substring，从上一次出现的后一个出发，依次下去，到最后
```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int strSize=s.size();
        if(strSize==0)
            return 0;
        
        string subString;
        subString=s[0];
        int longestLength=1,currentLongestLen=1;
        for(int i=1;i<strSize;i++)
        {
            int foundPos=subString.find(s[i]);
            if(foundPos==subString.npos)
            {
                currentLongestLen+=1;
                subString+=s[i];
            }
            else
            {
                subString=subString.substr(foundPos+1)+s[i];
                currentLongestLen=subString.size();
            }
            if(currentLongestLen>longestLength)longestLength=currentLongestLen;
            
        }
        return longestLength;
    }
};
```

------

### 4. 两个排序数组的中位数
> * 题目描述：给定两个大小为 m 和 n 的有序数组 nums1 和 nums2 。
请找出这两个有序数组的中位数。要求算法的时间复杂度为 O(log (m+n)) 。
你可以假设 nums1 和 nums2 均不为空。
nums1 = [1, 2]
nums2 = [3, 4]
中位数是 (2 + 3)/2 = 2.5
> * 解题思路：这个...不会诶... O(n)的话好处理，新建一个vector依次存储就好
```cpp
class Solution {
public:
	double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
		int len1 = nums1.size();
		int len2 = nums2.size();
		vector<int> res(len1 + len2);
		int index = 0;
		int i1 = 0;
		int i2 = 0;
		while(i1 < len1 && i2 < len2)
		{
			if(nums1[i1] < nums2[i2])
			{
				res[index++] = nums1[i1++];
			}
			else
			{
				res[index++] = nums2[i2++];
			}
		}
		while(i1 < len1)
		{
			res[index++] = nums1[i1++];
		}
		while(i2 < len2)
		{
			res[index++] = nums2[i2++];
		}
		int num = len1 + len2;
		if(num % 2 == 1)
		{
			return res[num / 2];
		}
		else
		{
			int i = num / 2;
			return double(res[i - 1] + res[i]) / 2.0f;
		}
	}
};
```
-----
### 5. 最长回文子串

> * 题目描述：给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为1000。
> * 解题思路：中心扩展算法，即找到中心，两边镜像查找，如果有出现不同即停止，记录当前长度，从0开始，依次往右循环。(O(n^2))
```cpp
int  mirrorToExpand(string s,int i,int j)
{
    int maxMirrorLen=0;
    if(i==j) maxMirrorLen=-1;
    while(i>=0&&i<s.length()&&j>=0&&j<s.length()&&s[i]==s[j])
    {
        i--;
        j++;
        maxMirrorLen+=2;
    }
    return maxMirrorLen;
}
class Solution {
public:
    string longestPalindrome(string s) {
        int globeMaxLen=0;
        string globeMaxSubStr;
        int judge=0;
        for(int i=0;i<s.length();i++)
        {
            int curOddMaxLength,curEvenMaxLength;
            curOddMaxLength=mirrorToExpand(s,i,i);
            curEvenMaxLength=mirrorToExpand(s,i,i+1);
            
            if(curOddMaxLength>globeMaxLen)
            {
                globeMaxLen=curOddMaxLength;
                globeMaxSubStr=s.substr(i-curOddMaxLength/2,globeMaxLen);
            }
            if(curEvenMaxLength>globeMaxLen)
            {
                globeMaxLen=curEvenMaxLength;
                globeMaxSubStr=s.substr(i-(curEvenMaxLength-2)/2,globeMaxLen);
            }
            
        }
        return globeMaxSubStr;
    }

};
```
-----
### 6.Z字形变换

> * 题目描述:将字符串 "PAYPALISHIRING" 以Z字形排列成给定的行数：
P   A   H   N
A P L S I I G
Y   I   R
之后从左往右，逐行读取字符："PAHNAPLSIIGYIR"
实现一个将字符串进行指定行数变换的函数:
string convert(string s, int numRows);
> * 解题思路：[1,2,3,2...]，建立一个二维数组，按照1,2,3,2,...的顺序将对应的字母压入对应的vector中，然后从第一到第三个依次读取出来，组成返回字符串

```cpp
class Solution {
public:
    string convert(string s, int numRows) {
        if(s=="")
            return "";
        if(numRows==1)
            return s;
        vector<vector<char> > vec_chars(numRows,vector<char>());
        int order=-1;
        int direction=1;
        for(int i=0;i<s.size();i++)
        {
            order+=direction;
            vec_chars[order].push_back(s[i]);
            if((direction==1&&order==numRows-1)||(direction==-1&&order==0))
                direction*=-1;
        }
        string res="";
        for(int i=0;i<vec_chars.size();i++)
        {
            for(int j=0;j<vec_chars[i].size();j++)
            {
                res+=vec_chars[i][j];
            }
        }
        return res;
    }
};
```

------
### 7.反转整数
>* 给定一个 32 位有符号整数，将整数中的数字进行反转。
>* 基本思路：用长整形来存储反转之后的结果，如果得到的结果大于pow(2,31)，它只可能有一个结果，就是０
```cpp
class Solution {
public:
    int reverse(int x) {
        long int num2reverse=x;
        long int reverseX=0;
        while(num2reverse!=0)
        {
            reverseX=reverseX*10+abs(num2reverse)%10;
            num2reverse/=10;
        }
        if(reverseX>=pow(2,31))
            return 0;
        if(x<0)
            return -1*abs(reverseX);
        return reverseX;
    }
};
```
----
### 9.回文数
> * 题目描述：判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。
> * 解题思路：这个数不能被10整除，也不能小于０，直接生成它的回文数对比，简单粗暴
```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        if(x<0||(x%10==0&&x!=0))return false;
        int cp_x=x;
        int sum=0;
        while(cp_x>0)
        {
            sum=sum*10+cp_x%10;
            cp_x/=10;
        }
        if(x==sum)
            return true;
        return false;
    }
};
```
----
### 11. 盛最多水的容器
> * 给定 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。
说明：你不能倾斜容器，且 n 的值至少为 2。

> * 解题思路：双指针，左边和右边各一个，每次都计算两个之间所承载的水，如果体积大于之前的，替换当前为最优的，并挑选其中小的一侧往中间移动，实时移动更新
```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int i=0,j=height.size()-1,h=0,water=0;
        while(i<j)
        {
            h=min(height[i],height[j]);
            water=max(h*(j-i),water);
            while(height[i]==height[j])
            {
                i++;
            }
            
            if(height[i]<=h&&i<height.size())
                i++;
            
            while(height[j]<=h&&j>0)
                j--;
            
        }
        return water;
    }
};
```

----
###14. 最长公共前缀
> * 编写一个函数来查找字符串数组中的最长公共前缀。如果不存在公共前缀，返回空字符串 ""。
例子：
输入: ["flower","flow","flight"]
输出: "fl"
> * 解题思路：太简单了吧。直接依次循环找出来不就好了
```cpp

class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        if(strs.size()==0)
            return "";
        string prefixStr=strs[0];
        for(int i=1;i<strs.size();i++)
        {
            string curStr=strs[i],subStr="";
            for(int j=0;j<strs[i].length();j++)
            {
                if(prefixStr[j]==curStr[j])
                    subStr+=prefixStr[j];
                else
                    break;
            }
            prefixStr=subStr;
        }
        return prefixStr;
    }
};
```
----
###15. 三数之和
> * 题目描述：给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。
例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，
满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
> * 解题思路：先判断边界条件，对三个数进行排序，从第二个数开始到倒数第二个数作为中心数，这时只需要求解两数之和，two_sum=target-middle,如果左右相加小于two_sum，右数向右边移动，否则左数左移动，直到相等。其中使用了哈希表做重复性检验。
```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> res;
        if(nums.size()<3)
            return res;
        sort(nums.begin(),nums.end());
        unordered_map<string,int> hash_map_judge;
        for(int i=1;i<nums.size()-1;i++)
        {
            int left=i-1;
            int right=i+1;
            int target=-(nums[i]);
            while(left>=0&&right<nums.size())
            {
                // cout<<"before:"<<nums[left]<<" "<<nums[i]<<" "<<nums[right]<<endl;
                if(left!=i-1&&nums[left]==nums[left+1])
                {
                    left--;
                    continue;
                }
                else if(right!=i+1&& nums[right]==nums[right-1])
                {
                    right++;
                    continue;
                }
                // cout<<"after:"<<nums[left]<<" "<<nums[i]<<" "<<nums[right]<<endl;
                if(target==(nums[left]+nums[right]))
                {
                    string temp=to_string(nums[left])+to_string(nums[i])+to_string(nums[right]);
                    if(hash_map_judge.find(temp)==hash_map_judge.end())
                    {
                        hash_map_judge.insert(make_pair(temp,1));
                        vector<int> temp_res={nums[left],nums[i],nums[right]};
                        res.push_back(temp_res);

                    }
                    left--;
                    right++;
                }
                else if(target<(nums[left]+nums[right]))
                    left--;
                else
                    right++;
                
            }
        }
        return res;
    }
};
```
---
###17. 电话号码的字母组合
> * 给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。
给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
> * 解题思路：建立一个映射表，再DFS遍历一遍得到结果
```cpp
class Solution {
public:
    string letterMap[10]={
        " ",
        "",
        "abc",
        "def",
        "ghi",
        "jkl",
        "mno",
        "pqrs",
        "tuv",
        "wxyz"
    };
    vector<string> res;
    
    void findCombination(  string  digits,int index, const string &s)
    {
        if(index == digits.size()){
            res.push_back(s);
            return;
        }
        
        char c = digits[index];
        string letters = letterMap[c-'0'];
        for(int i = 0; i < letters.size();i++)
        {
            findCombination(digits,index+1,s+letters[i]);
        }
        return;
    }
    vector<string> letterCombinations(string digits) {
        if(!digits.size()) return res;
        findCombination(digits,0,"");
    return res;
    }
};
```
----

### 18.四数之和
> * 给定一个包含 n 个整数的数组 nums 和一个目标值 target，判断 nums 中是否存在四个元素 a，b，c 和 d ，使得 a + b + c + d 的值与 target 相等？找出所有满足条件且不重复的四元组。
给定数组 nums = [1, 0, -1, 0, -2, 2]，和 target = 0。
满足要求的四元组集合为：
[
  [-1,  0, 0, 1],
  [-2, -1, 1, 2],
  [-2,  0, 0, 2]
]
> * 四数之和只是在三数之和的基础上再嵌入一层循环

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> total;
        int n=nums.size();
        if(n<4)
        {
            return total;
        }
        sort(nums.begin(),nums.end());

        for(int i=0;i<n-3;i++)
        {
            if(i>0&&nums[i]==nums[i-1])continue;
            if(nums[i]+nums[i+1]+nums[i+2]+nums[i+3]>target)
                break;
            if(nums[i]+nums[n-1]+nums[n-2]+nums[n-3]<target)
                continue;
            for(int j=i+1;j<n-2;j++)
            {
                if(j>i+1&&nums[j]==nums[j-1])continue;
                if(nums[i]+nums[j]+nums[j+1]+nums[j+2]>target)break;
                if(nums[i]+nums[j]+nums[n-2]+nums[n-1]<target)continue;
                int left=j+1,right=n-1;
                while(left<right)
                {
                    int sum=nums[i]+nums[j]+nums[left]+nums[right];
                    if(sum>target)
                        right--;
                    else if(sum<target)
                        left++;
                    else
                    {
                        vector<int> fourForTarget;
                        fourForTarget.push_back(nums[i]);
                        fourForTarget.push_back(nums[j]);
                        fourForTarget.push_back(nums[left]);
                        fourForTarget.push_back(nums[right]);
                        total.push_back(fourForTarget);
                        do{left++;}while(nums[left]==nums[left-1]&&left<right);
                        do{right--;}while(nums[right]==nums[right+1]&&left<right);
                    }
                }
            }
        }
        return total;
    }
    

};
```
-----
### 19. 删除链表的倒数第N个节点
> * 给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。
示例：
给定一个链表: 1->2->3->4->5, 和 n = 2.
当删除了倒数第二个节点后，链表变为 1->2->3->5.
> * 双指针，第一个指针先提前走n步，然后再一起走，直到提前走的那个指针碰到末尾。前一个进行删除操作。
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* fast=head;
        ListNode* slow=head;
        ListNode * tempNode;
        for(int i=0;i<n;i++)
        {
            if(fast==NULL)
                return head;
            fast=fast->next;
        }
        if(fast==NULL)
        {
            tempNode=head;
            head=head->next;
            delete tempNode;
            return head;
        }
        while(fast->next!=NULL)
        {
            fast=fast->next;
            slow=slow->next;
        }
        tempNode=slow->next;
        slow->next=slow->next->next;
        return head;
        
    }
};
```
----
###21. 合并两个有序链表
> * 将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。
示例：
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4

> * 这个简单啦，直接合并两个不就行了嘛，新建一个链表，直接一个一个读取添加呀
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if(l1==NULL)
            return l2;
        else if(l2==NULL)
            return l1;
        ListNode* MergedList=new ListNode(min(l1->val,l2->val));
        ListNode* slideNode=MergedList;
        // MergedList->val=;
        if(l1->val>l2->val)
            l2=l2->next;
        else
            l1=l1->next;
        while(l1!=NULL&&l2!=NULL)
        {
            ListNode* new_node;
            if(l1->val>l2->val)
            {
                new_node=new ListNode(l2->val);
                l2=l2->next;
            }
            else
            {
                new_node=new ListNode(l1->val);
                l1=l1->next;
            }
            slideNode->next=new_node;
            slideNode=slideNode->next;
        }
        while(l1!=NULL)
        {
            ListNode* new_node;
            new_node=new ListNode(l1->val);
            l1=l1->next;
            
            slideNode->next=new_node;
            slideNode=slideNode->next;
        }
        while(l2!=NULL)
        {
            ListNode* new_node;
            new_node=new ListNode(l2->val);
            l2=l2->next;
            
            slideNode->next=new_node;
            slideNode=slideNode->next;
        }
        // free(slideNode);
        return MergedList;
    }
};
```
----
### 22. 括号生成
> * 给出 n 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且有效的括号组合。
例如，给出 n = 3，生成结果为：
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]
> * 递归呀，全排列，深度优先搜索
```cpp
class Solution {
public:
    void addingPar(vector<string> &v,string str,int n,int m)
    {
        if(m==0&&n==0)
        {
            v.push_back(str);
        }
        
        if(n>0)
        {
            addingPar(v,str+'(',n-1,m+1);
        }
        if(m>0)
        {
            addingPar(v,str+')',n,m-1);
        }
    }
    vector<string> generateParenthesis(int n) {
        vector<string> res;
        addingPar(res,"",n,0);
        return res;
    }
    
};
```

----
### 24. 两两交换链表中的节点
> * 给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。
示例:
给定 1->2->3->4, 你应该返回 2->1->4->3.
> * 按规则来就好，没什么的
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        ListNode* p=head;
        if(head==NULL)
            return head;
        if(head->next!=NULL)
            head=head->next;
        ListNode* before=p;
        int i=0;
        while(p!=NULL&&p->next!=NULL)
        {
            ListNode* later=p->next;
            p->next=p->next->next;
            later->next=p;
            if(i!=0)
            {
                before->next=later;
                before=p;
            }
            i++;
            p=p->next;
            
        }
        return head;
    }
};
```

----
### 31. 下一个排列
> *实现获取下一个排列的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。
如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。
必须原地修改，只允许使用额外常数空间。
以下是一些例子，输入位于左侧列，其相应输出位于右侧列。
1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1
> * 这个...不会诶...

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        if(nums.size()<=1)
            return;
        
        bool need_reverse=true;
        for(int i=nums.size()-1;i>0;i--)
        {
            int a_i=nums[i];
            int a_i_1=nums[i-1];
            if(a_i>a_i_1)
            {
                need_reverse=false;
                bool found=false;
                for(int j=i;j<nums.size();j++)
                {
                    if(a_i_1<nums[j]) continue;
                    else
                    {
                        nums[i-1]=nums[j-1];
                        nums[j-1]=a_i_1;
                        reverse(nums.begin()+i,nums.end());
                        found=true;
                        break;
                    }
                }
                if(!found)
                {
                    nums[i-1]=nums[nums.size()-1];
                    nums[nums.size()-1]=a_i_1;
                    reverse(nums.begin()+i,nums.end());
                }
                break;
            }
        }
        if(need_reverse)
        {
            reverse(nums.begin(),nums.end());
        }
    }
    
};
```
----

### 32. 最长有效括号
> * 给定一个只包含 '(' 和 ')' 的字符串，找出最长的包含有效括号的子串的长度。
示例 1:
输入: "(()"
输出: 2
解释: 最长有效括号子串为 "()"
> * 用堆栈，依次压入，再根据栈顶的符号来进行分情况讨论，可以累加的累加，可以乘以２的乘以２，之后在从堆中挑选出最大元素
```cpp
class Solution {
public:
    int longestValidParentheses(string s) {
        stack<string> parent_stack;
        for(int i=0;i<s.size();i++)
        {
            if(parent_stack.empty())
            {
                string s_i(1,s[i]);
                parent_stack.push(s_i);
                continue;
            }
            if(s[i]=='(')
            {
                parent_stack.push("(");
                continue;
            }
            
            if(parent_stack.top()==")")
            {
                string s_i(1,s[i]);
                parent_stack.push(s_i);
            }
            else if(parent_stack.top()=="(")
            {
                 
                parent_stack.pop();
                int length_combine=2;
                while(!parent_stack.empty() && parent_stack.top()!="(" && parent_stack.top()!=")" )
                {
                    
                    int last_length=atoi(parent_stack.top().c_str());
                    cout<<"parent_stack.top():"<<parent_stack.top()<<endl;
                    length_combine+=last_length;
                    parent_stack.pop();
                }
                
                //cout<<"length_combine:"<<length_combine<<endl;
                parent_stack.push(to_string(length_combine));
            }
            else
            {
                int length_combine=atoi(parent_stack.top().c_str());
                parent_stack.pop();
                if(parent_stack.empty()|| parent_stack.top()==")")
                {
                    parent_stack.push(to_string(length_combine));
                    string s_i(1,s[i]);
                    parent_stack.push(s_i);
                }
                else
                {

                    parent_stack.pop();
                    length_combine+=2;
                    cout<<"here i:"<<i<<"  lenth"<<length_combine<<endl;
                    while(!parent_stack.empty()&& parent_stack.top()!="(" && parent_stack.top()!=")" )
                    {
                        length_combine+=atoi(parent_stack.top().c_str());
                        parent_stack.pop();
                    }
                    parent_stack.push(to_string(length_combine));

                }
            }
        }
        
        int longest_valid_parent=0;
        while(!parent_stack.empty())
        {
            if(parent_stack.top()=="("||parent_stack.top()==")")
            {
                parent_stack.pop();
                continue;
            }
            int cur_length=atoi(parent_stack.top().c_str());
            
            if(cur_length>longest_valid_parent)
            {
                longest_valid_parent=cur_length;
                cout<<"cur_length:"<<cur_length<<endl;
            }
            parent_stack.pop();
        }
        return longest_valid_parent;
    }
};
```

> * 用dp 好像简单很多
If s[i] is '(', set longest[i] to 0,because any string end with '(' cannot be a valid one.
Else if s[i] is ')'
     If s[i-1] is '(', longest[i] = longest[i-2] + 2
     Else if s[i-1] is ')' and s[i-longest[i-1]-1] == '(', longest[i] = longest[i-1] + 2 + longest[i-longest[i-1]-2]

> *  code
```cpp
class Solution {
public:
    int longestValidParentheses(string s) {
       if (s.length() < 2) {
        return 0;
    }
    vector<int> dp(s.length(), 0);
    int max = 0;
    for (int i = 1; i < s.length(); i++) {
        int start = dp[i - 1];
        if (dp[i - 1] != i - 1) {
            start--;
        }
        if (s[start] == '(' && s[i] == ')') {
            if (start - 1 >= 0 && dp[start - 1] != start - 1) {
                dp[i] = dp[start - 1];
            }  else {
                dp[i] = dp[start];
            }
        } else {
            dp[i] = i;
        }
        if (i != dp[i] && max < (i - dp[i] + 1)) {
            max = (i - dp[i] + 1);
        }
    }
    return max;
    }
};
```
----
### 35. 搜索插入位置
> * 给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
> * 简单吧，直接找小于等于的那个序号呗。
```cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int res=-1;
        for(int i=0;i<nums.size();i++)
        {
            if(target<=nums[i])
            {
                res=i;
                break;
            }
        }
        if(res==-1)
            res=nums.size();
        return res;
    }
};
```
-----

###36. 有效的数独
> * 判断一个 9x9 的数独是否有效。只需要根据以下规则，验证已经填入的数字是否有效即可。
(1)数字 1-9 在每一行只能出现一次。
(2)数字 1-9 在每一列只能出现一次。
(3)数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。
> * 傻逼都能想到的方法，加个哈希表，直接按规则来呀！！！
```cpp
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        unordered_map<char,int> mapOne2Ten;
        for(int i=0;i<9;i++)
        {
            unordered_map<char,int>::iterator it;
            //testify every row is valid
            for(int j=0;j<=9;j++)
            {
                if(board[i][j]=='.')continue;
                if((it=mapOne2Ten.find(board[i][j]))==mapOne2Ten.end())
                {
                    mapOne2Ten.insert(make_pair(board[i][j],1));
                    
                }
                else
                    return false;
            }
            mapOne2Ten.clear();
            //testify every column
            for(int j=0;j<9;j++)
            {
                if(board[j][i]=='.')continue;
                if((it=mapOne2Ten.find(board[j][i]))==mapOne2Ten.end())
                {
                    mapOne2Ten.insert(make_pair(board[j][i],1));
                }
                else
                    return false;
            }
            mapOne2Ten.clear();

        }
        //testify every box
        for(int i=0;i<3;i++)
        {
            for(int j=0;j<3;j++)
            {
                
                for(int k=0;k<9;k++)
                {
                    unordered_map<char,int>::iterator it;
                    char testChr=board[3*i+k/3][3*j+k%3];
                    if(testChr=='.') continue;
                    if((it=mapOne2Ten.find(testChr))==mapOne2Ten.end())
                    {
                         mapOne2Ten.insert(make_pair(testChr,1));
                    }
                    else
                        return false;
                }
                mapOne2Ten.clear();
            }
            
        }
        return true;
    }
};
```
> * 所以肯定是有更精巧的方法的呀,虽然原理上其实真的差不多诶
```cpp
class Solution
{
public:
    bool isValidSudoku(vector<vector<char> > &board)
    {
        int used1[9][9] = {0}, used2[9][9] = {0}, used3[9][9] = {0};
        
        for(int i = 0; i < board.size(); ++ i)
            for(int j = 0; j < board[i].size(); ++ j)
                if(board[i][j] != '.')
                {
                    int num = board[i][j] - '0' - 1, k = i / 3 * 3 + j / 3;
                    if(used1[i][num] || used2[j][num] || used3[k][num])
                        return false;
                    used1[i][num] = used2[j][num] = used3[k][num] = 1;
                }
        
        return true;
    }
};
```

### 39. 组合总和
> * 给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
candidates 中的数字可以无限制重复被选取。
输入: candidates = [2,3,6,7], target = 7,
所求解集为:
[
  [7],
  [2,2,3]
]
> * 和我的猜想一样嘛，递归，深度优先搜索
```cpp
class Solution {
public:
    void fun(vector<vector<int>>& re,vector<int>& candidates, int target,vector<int>& a,int i)
    {
        if(target==0)
        {
            re.push_back(a);
            return;
        }else if(target<0)
        {
            return;
        }
        for(int j=i;j<candidates.size();++j)
        {
        	a.push_back(candidates[j]);
        	fun(re,candidates,target-candidates[j],a,j);
        	a.pop_back();
		}
        
    }
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> result;
        vector<int> a;
        fun(result,candidates,target,a,0);
        return result;
    }
    
};
```
---
###40. 组合总和 II
> * 给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
candidates 中的每个数字在每个组合中只能使用一次。
输入: candidates = [10,1,2,7,6,1,5], target = 8,
所求解集为:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]
> * 和上一题差不多，多一个判定条件
```cpp
class Solution {
public:
    
    void combination(vector<int> candidates,vector<vector<int>>& res,vector<int> follow,int target,int begin)
    {
        if(target==0)
        {
            res.push_back(follow);
            return;
        }
        else if(target>0)
        {
            
            for(int i=begin;i<candidates.size();i++)
            {
                if(candidates[i]>target) return;
                if(i==begin||candidates[i]!=candidates[i-1])
                {
                    //if(i!=0) cout<<i<<" "<<candidates[i]<<" "<<candidates[i-1]<<endl;
                    vector<int> tmpfollow=follow;
                    int tmpTarget=target;
                    tmpfollow.push_back(candidates[i]);
                    tmpTarget=tmpTarget-candidates[i];
                    if(tmpTarget<0) return;
                    combination(candidates,res,tmpfollow,tmpTarget,i+1);
                }
                
            }
        }
        else
            return;
    }

    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<vector<int>> res;
        vector<int> follow;
        if(candidates.size()==0)
            return res;
        
        sort(candidates.begin(),candidates.end());
        combination(candidates,res,follow,target,0);
        return res;
    }
};
```
-----

### 41. 缺失的第一个正数
> * 给定一个未排序的整数数组，找出其中没有出现的最小的正整数。
示例 1:
输入: [1,2,0]
输出: 3
说明:
你的算法的时间复杂度应为O(n)，并且只能使用常数级别的空间。
> * 应用数组和值和对应的下表替换，能得到一个排列值和下标一致的数组，如果不一致，则说明当前值是需要找的第一个最小正整数
```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        for (int i = 0; i < nums.size(); i++) {
            int cur = i;
            while (nums[cur] > 0 && nums[cur] <= nums.size() &&
                  nums[cur] != cur + 1 && nums[nums[cur] - 1] != nums[cur]) {
                swap(nums[nums[cur] - 1], nums[cur]);
            }
        }
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        return nums.size() + 1;
    }
};
```
----
### 42. 接雨水
> * 给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。
上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。 感谢 Marcos 贡献此图。
示例:
输入: [0,1,0,2,1,0,1,3,2,1,2,1]
输出: 6
> * 从左到右。依次得到左边最大的数，从右到左，依次得到右边最大的数字，每一个位置上得到的左右最小的那个减去当前的列的实体即为盛水的体积。
```
class Solution {
public:
    int trap(vector<int>& height) {
        if(height.size()<=2)
            return 0;
        vector<int> left2right(height.size(),0),right2left(height.size(),0);
        int left_max=0,right_max=0;
        left2right[0]=height[0];
        right2left[height.size()-1]=height[height.size()-1];
        for(int i=1;i<height.size();i++)
        {
            left2right[i]=max(height[i],left2right[i-1]);
            right2left[height.size()-i-1]=max(height[height.size()-i-1],right2left[height.size()-i]);
        }
        int total_sum=0;
        for(int i=0;i<height.size();i++)
        {
            total_sum+=min(left2right[i],right2left[i])-height[i];
        }
        return total_sum;
    }
};
```

> * 以上这种空间复杂度为O(n),以下空间复杂度为Ｏ(1)
```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int l = 0, r = height.size() - 1;
        int res = 0;
        int level = 0;
        while(l < r)
        {
            int lower = height[l] < height[r] ? height[l++] : height[r--];
            level = max(lower, level);
            res += level - lower;
        }
        return res;
    }
};
```
----
---