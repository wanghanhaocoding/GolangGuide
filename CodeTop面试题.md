# CodeTop面试题

# [3.无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/description/)（字符串、滑动窗口）

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

# [146.LRU缓存机制](https://leetcode.cn/problems/lru-cache/description/)（哈希表、链表）

```Go
type entry struct{
    key,value int 
}

type LRUCache struct {
    capacity int //缓存的最大数量
    list *list.List //指向双向链表的指针
    keyToNode map[int]*list.Element //map根据key快速查找双向链表中的元素
}

func Constructor(capacity int) LRUCache {
    return LRUCache{capacity,list.New(),map[int]*list.Element{}}
}

func (c *LRUCache) Get(key int) int {
    node := c.keyToNode[key]
    if node == nil { //没有这本书
        return -1
    }
    c.list.MoveToFront(node) //把这本书放在最上面
    return node.Value.(entry).value //node.Value抓换成entry类型，然后取出V
}

func (c *LRUCache) Put(key int, value int)  {
    if node := c.keyToNode[key];node != nil { //有这本书
        node.Value = entry{key,value} //更新
        c.list.MoveToFront(node) //把这本书放在最上面
        return
    }
    c.keyToNode[key] = c.list.PushFront(entry{key,value}) //新书，放在最上面
    if len(c.keyToNode) > c.capacity {
        delete(c.keyToNode,c.list.Remove(c.list.Back()).(entry).key) //去掉最后一本书
    }   
}
```

时间复杂度：所有操作均为 O(1)。

空间复杂度：O(min(*p*,*capacity*))，其中 *p* 为 put 的调用次数。

# [206.反转链表](https://leetcode.cn/problems/reverse-linked-list/description/)（哈希表）

```Go
func reverseList(head *ListNode) *ListNode {
    var pre,cur *ListNode = nil,head
    for cur != nil {
        nxt := cur.Next
        cur.Next = pre
        pre = cur
        cur = nxt
    }
    return pre
}
```

时间复杂度：O(*n*)，其中 *n* 为链表节点个数。

空间复杂度：O(1)，仅用到若干额外变量。

# [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)（二分）

```Go
func findKthLargest(nums []int, k int) int {
    TopKSplit(nums,len(nums) - k,0,len(nums) - 1)
    return nums[len(nums) - k]
}

func TopKSplit(nums []int,k,start,stop int){
    if start < stop{
        index := Parition(nums,start,stop) //选择第一个数，不要求全部有序，二分法
        if index == k{
            return
        }else if index < k{
            TopKSplit(nums,k,index + 1,stop)
        }else{
            TopKSplit(nums,k,start,index - 1)
        }
    }
}

func Parition(nums []int,start,stop int) int {
    if start >= stop {
        return -1
    }
    pivot := nums[start]
    l,r := start,stop
    for l < r{
        for l < r && nums[r] >= pivot {
            r --
        }
        nums[l] = nums[r]
        for l < r && nums[l] < pivot{
            l ++
        }
        nums[r] = nums[l]
    }
    nums[l] = pivot
    return l
}
```

时间复杂度：O(n)

空间复杂度：O(logn)，递归使用栈空间的空间代价的期望为 O(logn)。