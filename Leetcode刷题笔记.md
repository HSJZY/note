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