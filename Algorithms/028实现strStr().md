# 028 实现 strStr()

## 题目

实现 strStr() 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

示例 1:

```txt
输入: haystack = "hello", needle = "ll"
输出: 2
```
示例 2:

```txt
输入: haystack = "aaaaa", needle = "bba"
输出: -1
```

说明:

当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。

对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。

***

## 思路

1. 分析题目，给定两个字符串，让找到其中一个字符串，在另一个字符串中出现的第一个位置下标，默认为0，不存在，返回-1，类似于实现java里面indexOf()功能;
2. 首先可以使用暴力解法，最简单的暴力解法，就是每次遍历haystack字符串里needle 字符串长度的字符，判断和needle是否相等，如果相等，就返回首字母出现的位置。如果不相等，就右移一个字符，接着重复去比对判断，如果都不相等，则返回-1；
3. 还可以使用kmp，sunday等解法，降低时间复杂度，来进行优化，这部分算法后续研究透彻后，会重新进行详细解释；

***

## 复杂度分析

KMP算法：
- 时间复杂度 $O(N)$ 
- 空间复杂度 $O(N)$ 

***

## 代码

### 附上java,python3,c++的实现代码

#### java方法

- 暴力法

```java
public int strStr(String haystack, String needle) {
    int strLen = haystack.length(), subLen = needle.length();
    if (subLen == 0) return 0;
    for (int index = 0; index < strLen; index++) {
        /* 首字符不匹配则跳过 */
        if (haystack.charAt(index) != needle.charAt(0)) continue;
        /* 超出边界，无法获取结果 */
        if (index + subLen > strLen) break;
        int tmp = index + 1;
        /* 内循环判断是否相等，若完全相等，则 tmp == index + subLen */
        for(;tmp < index + subLen; tmp++)
            if (haystack.charAt(tmp) != needle.charAt(tmp - index)) break;
        if (tmp == index + subLen) return index;
    }
    return -1;
}
```

- KMP

```java
public int strStr(String haystack, String needle) {
    int strLen = haystack.length(), subLen = needle.length();
    if (subLen == 0) return 0;
    if (strLen == 0) return -1;
    // 构建状态机
    int[][] FSM = new int[subLen][256];
    int X = 0, matchChar = 0;
    for (int i = 0; i < subLen; i++) {
        matchChar = (int) needle.charAt(i);
        for (int j = 0; j < 256; j++) {
            // 当前状态 + 匹配失败字符 = 孪生词缀状态 + 匹配字符
            FSM[i][j] = FSM[X][j]; 
        }
        FSM[i][matchChar] = i + 1;
        if (i > 1) {
            // 下一孪生前缀状态 = X + matchChar
            X = FSM[X][matchChar];
        }
    }
    // 匹配子串
    int state = 0;
    for (int i = 0; i < strLen; i++) {
        state = FSM[state][haystack.charAt(i)];
        if (state == subLen) {
            return i - subLen + 1;
        }
    }
    return -1;
}
```

#### python3方法

- KMP

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        nxt = [0]*(len(needle)+10)
        def get_nxt(s):
            k ,i = -1, 0
            nxt[0] = -1
            while i<len(s):
                if k==-1 or s[i]==s[k]:
                    i+=1
                    k+=1
                    nxt[i] = k
                else:
                    k = nxt[k]
        def match(s1, s2):
            get_nxt(s2)
            i = k = 0
            while i<len(s1) and k<len(s2):
                if k==-1 or s1[i]==s2[k]:
                    k+=1
                    i+=1
                else:
                    k=nxt[k]
            return i-k if k==len(s2) else -1

        return match(haystack, needle) if len(needle) else 0
```

- sunday

```python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
    
        # Func: 计算偏移表
        def calShiftMat(st):
            dic = {}
            for i in range(len(st)-1,-1,-1):
                if not dic.get(st[i]):
                    dic[st[i]] = len(st)-i
            dic["ot"] = len(st)+1
            return dic
        
        # 其他情况判断
        if len(needle) > len(haystack):return -1
        if needle=="": return 0
       
        # 偏移表预处理    
        dic = calShiftMat(needle)
        idx = 0
    
        while idx+len(needle) <= len(haystack):
            
            # 待匹配字符串
            str_cut = haystack[idx:idx+len(needle)]
            
            # 判断是否匹配
            if str_cut==needle:
                return idx
            else:
                # 边界处理
                if idx+len(needle) >= len(haystack):
                    return -1
                # 不匹配情况下，根据下一个字符的偏移，移动idx
                cur_c = haystack[idx+len(needle)]
                if dic.get(cur_c):
                    idx += dic[cur_c]
                else:
                    idx += dic["ot"]
            
        
        return -1 if idx+len(needle) >= len(haystack) else idx
```

#### c++方法

- KMP

```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int i = 0;
        for(int j = 0; j < nums.size(); ++ j)
        {
            if(nums[j] != val)
            {
                nums[i] = nums[j];
                ++ i;
            }
        }
        return i;
    }
};
```
