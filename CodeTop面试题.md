# CodeTop面试题

# 滑动窗口

## [3.无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/description/)

```Go
func lengthOfLongestSubstring(s string) int {
    ans := 0
    cnt := [128]int{} //大部分常见的字符，如英文字母（大小写）、数字、标点符号等都包含在 ASCII 字符集中。ASCII 字符集共有 128 个字符
    left := 0
    for right, c := range s{
        cnt[c] ++
        for cnt[c] > 1 { //说明窗口内有重复字母
            cnt[s[left]] --  //移除窗口左端点字母（是重复的）
            left ++ //缩小窗口
        }
        ans = max(ans,right - left + 1) //窗口移动一次，更新窗口长度的最大值
    }
    return ans 
}
func lengthOfLongestSubstring(s string) int {
    ans := 0
    window := [128]bool{} //初始化布尔数组
    left := 0
    for right, c := range s{
        for window[c] { //如果窗口内包含元素，将true置为false,并缩小窗口
            window[s[left]] = false
            left ++ //缩小窗口
        }
        window[c] = true //加入元素
        ans = max(ans,right - left + 1) //每次循环取最长子串
    }
    return ans
}
```

时间复杂度：O(n)，其中 n 为 s 的长度。注意 left 至多增加 n 次，所以整个二重循环至多循环 O(n) 次。

空间复杂度：O(∣Σ∣)，其中 ∣Σ∣ 为字符集合的大小，本题中字符均为 ASCII 字符，所以 ∣Σ∣≤128。