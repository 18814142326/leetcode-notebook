## 二分排序

```java
public int searchInsert(int[] nums, int target) {
    int left = 0;
    int right = nums.length-1;  // 记得减一
    while(left <= right){
        int mid = (right + left)/2; // 获取mid
        if(nums[mid] == target){
            return mid;
        }else if(target < nums[mid]){
            right = mid - 1;    // 赋值给right，应该是mid的前一个
        }else{
            left = mid + 1;     // 赋值给left，应该是mid的后一个
        }
    }
    return left;    // 最后return left
}
```



```java
// 如果要返回 right(但无必要)
public int searchInsert(int[] nums, int target) {
        int left = 0;
        int right = nums.length;
        int mid;
        while(right > left){
            mid = (right-1 + left)/2;
            if(nums[mid] == target){
                return mid;
            }else if(target < nums[mid]){
                right = mid;
            }else{
                left = mid+1;
            }
        }
        return right;
}
```

## 三数之和

思路：先对数组进行排序。暴力枚举的思路是，for循环三次，找到所有不重复的三元组，并逐一计算总和是否为零。这样的时间复杂度为$O(n^3)$
用双指针优化的思路为：首先正常无重复地枚举第一个，把第二和第三个加起来的数等于第一个的负数，用两个指针枚举他们。一个是左指针，从0开始，枚举第二个，右指针枚举第三个。保证左指针在右指针左边，即左<右。因为如果右=左是无意义的，如果右<左，即使满足条件，也不是重复的三元组了。



	给你一个包含 n 个整数的数组nums，判断nums中是否存在三个元素 a，b，c
	使得a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。
代码：

```java
public List<List<Integer>> threeSum(int[] nums) {
    //int i = Integer.;
    int n = nums.length;
    Arrays.sort(nums);  // 先把数组排序
    List<List<Integer>> ans = new ArrayList<List<Integer>>();
    // 枚举 a
    for (int first = 0; first < n; ++first) {
        // 需要和上一次枚举的数不相同
        if (first > 0 && nums[first] == nums[first - 1]) {
            continue;
        }
        // c 对应的指针初始指向数组的最右端
        int third = n - 1;
        int target = -nums[first];
        // 枚举 b
        for (int second = first + 1; second < n; ++second) {
            // 需要和上一次枚举的数不相同
            if (second > first + 1 && nums[second] == nums[second - 1]) {
                continue;
            }
            // 需要保证 b 的指针在 c 的指针的左侧
            while (second < third && nums[second] + nums[third] > target) {
                --third;
            }
            // 如果指针重合，随着 b 后续的增加
            // 就不会有满足 a+b+c=0 并且 b<c 的 c 了，可以退出循环
            if (second == third) {
                break;
            }
            if (nums[second] + nums[third] == target) {
                List<Integer> list = new ArrayList<Integer>();
                list.add(nums[first]);
                list.add(nums[second]);
                list.add(nums[third]);
                ans.add(list);
            }
        }
    }
    return ans;
}
```


## 最大子序和

`题目：给你一个整数数组 nums ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。`

这道题用暴力求解会超过时间，考虑使用动态规划的方法。

$f(i)$代表以第$i$个数字结尾的连续子数组（不代表有$i$个数），用动态规划的思路去考虑，只需要考虑$nums[i]$是单独作为子数组更大，还是加入前面的$f(i-1)$的值会变得更大。所以可以写成：
$$
    f(i)=max\{f(i-1) + nums[i], nums[i]\}
$$
Java代码实现：
```java
public int maxSubArray(int[] nums) {
    int pre = 0;
    int ans = nums[0];
    int n = nums.length;
    for(int i = 0; i < n; i++){
        pre = Math.max(pre+nums[i], nums[i]);
        ans = Math.max(pre, ans);
    }
    return ans;
}
```
## 字符串最长公共前缀
示例：
```
输入：strs = ["flower","flow","flight"]
输出："fl"
```
思路：只需要用第一个字符串和剩余的所有逐一比较即可。

第一种思路是横向比较，先比较第一个和第二个的公共子串，再用这个公共子串与其他字符串进行比较。用$f(S_1,...,S_n)$表示字符串$S_1,...,S_n$的最长公共前缀。所以可以表达成：
$$
f(S_1,...,S_n)=f(f(f(S_1,S_2),S_3),...,S_n)
$$

Java代码表示：
```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        }
        String prefix = strs[0];
        int count = strs.length;
        for (int i = 1; i < count; i++) {
            prefix = longestCommonPrefix(prefix, strs[i]);
            if (prefix.length() == 0) {
                break;
            }
        }
        return prefix;
    }

    public String longestCommonPrefix(String str1, String str2) {
        int length = Math.min(str1.length(), str2.length());
        int index = 0;
        while (index < length && str1.charAt(index) == str2.charAt(index)) {
            index++;
        }
        return str1.substring(0, index);
    }
}
```
先写一个通用的，用于比较两个字符串的方法，再在主方法中循环调用。

## KMP算法
```
问题：给出一个模板字符串和长字符串，要求在长字符串中匹配模板字符串.

如果匹配成功，则返回长字符串中第一次出现的位置。否则返回-1。
```

设长字符串的指针为$i$,长度为$n$。

模板字符串指针为$j$,长度为$m$。

两个字符串逐一对比，如果有一个字符对比错误，自然是要回退指针的。如果是暴力解法，$i$和$j$都会傻傻的退到开始的位置。这样，时间复杂度为$O(mn)$

KMP算法会判断，模板字符串当前对比的字符前缀，与哪个字符的前缀是相同的，这样，就可以回退到该字符处，继续进行对比，而不是傻傻的从0开始。因为如果具有相同的前缀，可以省略前缀部分的对比。

如何知道哪个字符与自己的前缀一样呢？KMP会初始化一个长度为$m$的$next$数组，存放了模板字符串中每一个字符匹配错误后应该回退到哪个字符继续进行匹配。

$next$数组的创建规则为：模板字符串自己与自己匹配，$i=1,j=0$，如果
$$
pat[i]==pat[j]
$$

$i,j$同时加一，否则的话：$j=next[j-1]$，一直到$j=0$。

初始化好$next$数组之后，就可以进行匹配了。

Java代码：
```java
public static int strStr1(String haystack, String needle) {
    int n = haystack.length(), m = needle.length();
    if(m>n) return -1;
    if(n==0 || m==0) return 0;

    char[] str = haystack.toCharArray();
    char[] pat = needle.toCharArray();

    // 初始化next数组
    int []next = new int[m];
    for(int i=1, j=0; i < m; i++){
        // 先使用while循环判断字符是否不相同
        // 直到出现相同或者模板字符串的指针回退到0为止
        while(j > 0 && pat[i] != pat[j]){
            j = next[j-1];
        }
        // 如果是因为出现了相同的字符而退出while循环
        // 则继续下一个判断
        if(pat[i] == pat[j]){
            j++;
        }
        // 对next数组赋值
        next[i] = j;
    }


    // 开始匹配
    /* 
    "mississippi"
    "issip"
        */
    for(int i = 0, j = 0; i < n; i++){
        // 先使用while循环判断字符是否不相同
        // 直到出现相同或者模板字符串的指针回退到0为止
        while(j > 0 && pat[j] != str[i]){
            j = next[j];
        }
        // 如果是因为出现了相同的字符而退出while循环
        // 则继续下一个判断
        if(pat[j] == str[i]){
            j++;
        }
        // 如果已经对最后一个字符进行了判断，则返回
        if(j == m) return i-(m-1);
    }
    return -1;
}
```


## 435. 无重叠区间
```
给定多个区间，计算让这些区间互不重叠所需要移除区间的最少个数。起止相连不算重叠。

输入: intervals = [ [1,2], [1,2], [1,2] ]
输出: 2
解释: 你需要移除两个 [1,2] 来使剩下的区间没有重叠。
```

这道题可以比喻成有n个会议，区间就是会议的起止时间，如何安排会议的顺序，让可以进行的会议最多？这题使用贪心的策略，先按照区间的结束值从低到高排序，然后再依次选择无重复和无重叠的区间即可。

要注意Java解决，需要用`Arrays.sort()`方法，传入要排序的二维数组和重写的`Comparator<>()`类的`compare`方法。泛型选择二维数组里面的一维数组，这里使用`Comparator<int[]>()`,记得背一下

```java
public int eraseOverlapIntervals(int[][] intervals) {
    if (intervals.length == 0) {
        return 0;
    }

    Arrays.sort(intervals, new Comparator<int[]>() {
        public int compare(int[] interval1, int[] interval2) {
            return interval1[1] - interval2[1];
        }
    });

    int n = intervals.length;
    int right = intervals[0][1];
    int ans = 1;
    for (int i = 1; i < n; ++i) {
        if (intervals[i][0] >= right) {
            ++ans;
            right = intervals[i][1];
        }
    }
    return n - ans;
}
```

## 双指针--167. 两数之和 II - 输入有序数组

```
在一个增序的整数数组里找到两个数，使它们的和为给定值。已知有且只有一对解。
```

正常的思路会使用暴力求解，两个for循环就能找出，时间复杂度为$O(n^2)$。

这道题考虑使用双指针。$l$指针指向数组最左边，$r$指针指向数组最右边。设$sum=num(l)+num(r)$。如果$sum$比$target$小，证明两个数需要变大，才能让结果更靠近目标值，这时让$l$指针右移。反之，让$r$指针左移，一直到找到目标值为止。

```java
public int[] twoSum(int[] numbers, int target) {
    int n = numbers.length;
    int l = 0, r = n - 1;
    int []ans = new int[2];
    
    while(l < r){
        if(numbers[l] + numbers[r] == target){
            ans[0] = l + 1;
            ans[1] = r + 1;
            return ans;
        }else if(numbers[l] + numbers[r] > target){
            // 需要减少sum，让其中一个数减少，即指针r往左移
            r--;
        }else{
            // 需要增加，指针l往右移
            l++;
        }
    }
    return ans;
}
```

## 快慢指针
### 检测链表是否有环

```
输入是一个链表，输出是链表的一个节点。如果没有环路，返回一个空指针。
```
这类问题有一个通用的解法，就是快慢指针（距离尾部第K个节点、寻找环入口、寻找公共尾部入口等题目都能用这个套路解题）

快指针每次走两步，慢指针每次走一步，如果无环，$fast$会变为空指针，如果有环，$fast$一定会追上$slow$，当$fast$和$slow$重合之后，把$slow$重置为头指针（从头开始走），$fast$步长变为$1$。当$fast$与$slow$再次重合的时候，就是环的入口。