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

# [15. 三数之和](https://leetcode.cn/problems/3sum/)（双指针）

```Go
func threeSum(nums []int) (ans [][]int) {
    slices.Sort(nums)
    n := len(nums)
    for i,x := range nums[:n - 2]{
        if i > 0 && x == nums[i - 1]{ //跳过重复数字
            continue
        }
        if x + nums[i + 1] + nums[i + 1] > 0 { //优化一
            break
        }
        if x + nums[n - 2] + nums[n - 1] < 0{
            continue
        }
        j,k := i + 1,n - 1
        for j < k{
            s := x + nums[j] + nums[k]
            if s > 0{
                k --
            } else if s < 0{
                j ++
            }else{ //三数之和为0
                ans = append(ans,[]int{x,nums[j],nums[k]})
                for j ++;j < k && nums[j] == nums[j - 1]; j ++{}
                for k --;k > j && nums[k] == nums[k + 1]; k --{}
            }
        }
    }
    return
}
```

时间复杂度：O(n2)，其中 n 为 nums 的长度。排序 O(nlogn)。外层循环枚举第一个数，就变成 167. 两数之和 II - 输入有序数组 了，做法是 O(n) 双指针。所以总的时间复杂度为 O(n2)。

空间复杂度：O(1)。返回值不计入，忽略排序的栈开销。

# [53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/)（动态规划）

```Go
func maxSubArray(nums []int) int {
    // 初始化最大子数组的和为数组的第一个元素
    maxSum := nums[0]
    for i := 1; i < len(nums); i++ {
        // 如果将当前元素添加到前一个元素所在的子数组中能使该子数组的和增大，
        // 则更新当前元素的值为该子数组的和，将当前元素扩展到前一个元素所在的子数组中
        if nums[i]+nums[i-1] > nums[i] { 
            nums[i] += nums[i-1]
        }
        // 如果当前元素所在的子数组（可能是经过上述扩展后的子数组）的和大于之前记录的最大子数组和，
        // 则更新最大子数组和为当前元素所在子数组的和
        if nums[i] > maxSum { 
            maxSum = nums[i]
        }
    }
    return maxSum
}
```

时间复杂度：O(n)

空间复杂度：O(n)

# [912. 排序数组](https://leetcode.cn/problems/sort-an-array/)（分治，排序）

```Go
func sortArray(nums []int) []int {
    // 定义一个内部函数 quick，用于实现快速排序
    var quick func(nums []int, left, right int) []int

    // 实现快速排序的递归函数
    quick = func(nums []int, left, right int) []int {
        // 当左边界小于右边界时，继续排序
        for left < right {
            // 随机选择主元（pivot），避免最坏情况的时间复杂度
            pivotIndex := left + rand.Intn(right-left+1)
            // 将主元交换到数组的最左边
            nums[pivotIndex], nums[left] = nums[left], nums[pivotIndex]
            pivot := nums[left] // 主元的值
            i, j := left, right // 初始化两个指针 i 和 j

            // 分区操作：将小于主元的元素放在左边，大于主元的元素放在右边
            for i < j {
                // 从右向左找到第一个小于主元的元素
                for i < j && nums[j] >= pivot {
                    j--
                }
                // 从左向右找到第一个大于主元的元素
                for i < j && nums[i] <= pivot {
                    i++
                }
                // 交换这两个元素
                nums[i], nums[j] = nums[j], nums[i]
            }

            // 将主元放到正确的位置（i 和 j 相遇的位置）
            nums[i], nums[left] = nums[left], nums[i]

            // 尾递归优化：先处理较短的子数组，减少递归深度
            if i-left < right-i {
                // 如果左子数组较短，先排序左子数组
                quick(nums, left, i-1)
                // 调整 left，准备在下一轮循环中处理右子数组
                left = i + 1
            } else {
                // 如果右子数组较短，先排序右子数组
                quick(nums, i+1, right)
                // 调整 right，准备在下一轮循环中处理左子数组
                right = i - 1
            }
        }
        return nums
    }

    // 调用快速排序函数，对整个数组进行排序
    return quick(nums, 0, len(nums)-1)
}
```

时间复杂度：O(nlogn)

空间复杂度：O(logn)

# [21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)（迭代）

```Go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func mergeTwoLists(list1 *ListNode, list2 *ListNode) *ListNode {
    // 创建一个哨兵节点，它的Next指针将指向合并后的链表的头节点
    dummy := ListNode{}
    
    // cur 是一个指针，指向新链表的最后一个节点
    cur := &dummy
    
    // 遍历两个链表，直到其中一个链表遍历完
    for list1 != nil && list2 != nil {
        // 比较两个链表当前节点的值
        if list1.Val < list2.Val {
            // 如果list1的当前节点值较小，将其连接到新链表的末尾
            cur.Next = list1
            // 移动list1的指针到下一个节点
            list1 = list1.Next
        } else {
            // 如果list2的当前节点值较小或相等，将其连接到新链表的末尾
            cur.Next = list2
            // 移动list2的指针到下一个节点
            list2 = list2.Next
        }
        // 移动cur指针到新链表的末尾
        cur = cur.Next
    }
    
    // 如果list1还有剩余节点，将其全部连接到新链表的末尾
    if list1 != nil {
        cur.Next = list1
    } else {
        // 如果list2还有剩余节点，将其全部连接到新链表的末尾
        cur.Next = list2
    }
    
    // 返回合并后的链表的头节点，即哨兵节点的Next指针
    return dummy.Next
}
```

时间复杂度：O(*n*+*m*)，其中 *n* 为 *list*1 的长度，*m* 为 *list*2 的长度。

空间复杂度：O(1)。仅用到若干额外变量。

# [21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)（递归）

```Go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */

func mergeTwoLists(list1 *ListNode, list2 *ListNode) *ListNode {
    // 如果 list1 为空，直接返回 list2
    // 因为如果 list1 为空，剩下的 list2 已经是有序的，不需要再合并
    if list1 == nil {
        return list2
    }
    
    // 如果 list2 为空，直接返回 list1
    // 因为如果 list2 为空，剩下的 list1 已经是有序的，不需要再合并
    if list2 == nil {
        return list1
    }
    
    // 比较 list1 和 list2 的头节点的值
    if list1.Val < list2.Val {
        // 如果 list1 的头节点值较小，那么 list1 的头节点就是新链表的头节点
        // 接下来递归地将 list1.Next 和 list2 合并，并将结果接在 list1 的后面
        list1.Next = mergeTwoLists(list1.Next, list2)
        return list1
    } else {
        // 如果 list2 的头节点值较小或相等，那么 list2 的头节点就是新链表的头节点
        // 接下来递归地将 list1 和 list2.Next 合并，并将结果接在 list2 的后面
        list2.Next = mergeTwoLists(list1, list2.Next)
        return list2
    }
}
```

时间复杂度：O(n+m)，其中 n 为list1的长度，m 为list2的长度。

空间复杂度：O(n+m)。递归需要 O(n+m) 的栈空间。