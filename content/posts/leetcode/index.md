---
title: "🔥 LeetCode HOT 100"
date: 2023-03-23T09:50:47+08:00
weight: 2
tags: ["算法"]
categories: ["算法"]
---

脑子越来越不好使，刷点算法题提高点智商。       

<!--more-->    

## Easy 

### 回文链表

[题目内容](https://leetcode.cn/problems/palindrome-linked-list/)

#### 解题思路

快慢指针 + 反转链表。   

* 初始化快慢指针 slow、fast 指向 head，fast 每次走两步，slow 每次走一步，当 fast 走到最后，slow 位于中间位置，slow 在移动期间不断反转前半部分链表，反转后为 prev。   
* 判断链表长度的奇偶性，如果是奇数 (即此时 fast 不为 null)，slow 再向后移动一步，过滤掉中间的公共节点。    
* prev 和 slow 此时分别为原链表的前半部分（反转后）和后半部分的头节点，循环判断 prev 和 slow 的值，都相同则返回 true，否则结束循环返回 false。   

#### 代码实现

```ts
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

const isPalindrome = (head: ListNode | null): boolean => {
    let fast: ListNode | null = head;
    let slow: ListNode | null = head;
    let prev: ListNode | null = null;
    while(fast !== null && fast?.next !== null) {
      fast = fast?.next?.next;
      const next: ListNode | null = slow?.next;
      slow.next = prev;
      prev = slow;
      slow = next;
    }
    if (fast !== null) slow = slow?.next;

    while(slow !== null && prev !== null) {
      if (slow?.val !== prev?.val) return false;
      prev = prev?.next;
      slow = slow?.next;
    }
    return true;
};
```

### 翻转二叉树

[题目内容](https://leetcode.cn/problems/invert-binary-tree/)

#### 解题思路

递 🐢。    

#### 代码实现

```ts
/**
 * Definition for a binary tree node.
 * class TreeNode {
 *     val: number
 *     left: TreeNode | null
 *     right: TreeNode | null
 *     constructor(val?: number, left?: TreeNode | null, right?: TreeNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.left = (left===undefined ? null : left)
 *         this.right = (right===undefined ? null : right)
 *     }
 * }
 */

const invertTree = (root: TreeNode | null): TreeNode | null => {
  if (root) {
    [root.left, root.right] = [invertTree(root.right), invertTree(root.left)];
  }
  return root;
};
```

### 反转链表

[题目内容](https://leetcode.cn/problems/reverse-linked-list/)

#### 解题思路

迭代。    

#### 代码实现

```ts
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

const reverseList = (head: ListNode | null): ListNode | null => {
  let prev: ListNode | null = null;
  let curr: ListNode | null = head;
  while(curr) {
    const next: ListNode | null = curr.next;
    curr.next = prev;
    prev = curr;
    curr = next;
  }
  return prev;
};
```

### 多数元素

[题目内容](https://leetcode.cn/problems/majority-element/)      

#### 解题思路   

摩尔投票法: 两两对抗，票数抵消。         

<img src="https://oweqian.oss-cn-hangzhou.aliyuncs.com/leetcode/img_01.png" alt="" width="600" />     

#### 代码实现

```ts
const majorityElement = (nums: number[]): number => {
  let count: number = 1;
  let major: number = nums[0];
  for (let i = 1; i < nums.length; i++) {
    if (count === 0) {
      major = nums[i];
      count = 1
    } else if (major === nums[i]) {
      count += 1;
    } else {
      count -= 1;
    }
  }
  return major;
};
```

### 相交链表

[题目内容](https://leetcode.cn/problems/intersection-of-two-linked-lists/)

#### 解题思路

哈希。

#### 代码实现

```ts
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

const getIntersectionNode = (headA: ListNode | null, headB: ListNode | null): ListNode | null => {
    let setA = new Set<ListNode>();
    let curr = headA;
    while(curr !== null) {
        setA.add(curr);
        curr = curr.next;
    }
    curr = headB;
    while(curr !== null) {
        if (setA.has(curr)) return curr;
        curr = curr.next;
    }
    return null;
};
```

### 环形链表

[题目内容](https://leetcode.cn/problems/linked-list-cycle/)

#### 解题思路

快慢指针。       

#### 代码实现

```ts
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

const hasCycle = (head: ListNode | null): boolean => {
  let slow: ListNode | null = head;
  let fast: ListNode | null = head;
  while(fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;
    if (slow === fast) {
      return true;
    }
  }
  return false;
};
```

### 只出现一次的数字

[题目内容](https://leetcode.cn/problems/single-number/)

#### 解题思路

异或位运算。    

<img src="https://oweqian.oss-cn-hangzhou.aliyuncs.com/leetcode/img.png" alt="" width="600" />  

#### 代码实现

```ts
const singleNumber = (nums: number[]): number => {
    let num: number = nums[0];
    if (nums.length > 1) {
        for (let i = 1; i < nums.length; i++) {
            num ^= nums[i];
        }
    }
    return num;
};
```

### 买卖股票的最佳时机 

[题目内容](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)

#### 解题思路

贪心，取最左最小值，取最右最大值，得到的差值就是最大利润。      

#### 代码实现

```ts
const maxProfit = (prices: number[]): number => {
    if (prices.length === 0) return 0;
    let min: number = prices[0];
    let max: number = 0;
    for (let item of prices) {
        min = Math.min(min, item);
        max = Math.max(max, item - min);
    }
    return max;
};
```

### 二叉树的最大深度

[题目内容](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

#### 解题思路

dfs + 递 🐢。    

#### 代码实现

```ts
/**
 * Definition for a binary tree node.
 * class TreeNode {
 *     val: number
 *     left: TreeNode | null
 *     right: TreeNode | null
 *     constructor(val?: number, left?: TreeNode | null, right?: TreeNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.left = (left===undefined ? null : left)
 *         this.right = (right===undefined ? null : right)
 *     }
 * }
 */

const maxDepth = (root: TreeNode | null): number => {
    if (root === null) return 0;
    let leftDepth = maxDepth(root?.left);
    let rightDepth = maxDepth(root?.right);
    return Math.max(leftDepth, rightDepth) + 1;
};
```

### 对称二叉树

[题目内容](https://leetcode.cn/problems/symmetric-tree/)

#### 解题思路

递 🐢。    

#### 代码实现

```ts
/**
 * Definition for a binary tree node.
 * class TreeNode {
 *     val: number
 *     left: TreeNode | null
 *     right: TreeNode | null
 *     constructor(val?: number, left?: TreeNode | null, right?: TreeNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.left = (left===undefined ? null : left)
 *         this.right = (right===undefined ? null : right)
 *     }
 * }
 */

const isSymmetric = (root: TreeNode | null): boolean => {
    const check = (p: TreeNode | null, q: TreeNode | null): boolean => {
        if (!p && !q) return true;
        if (!p || !q) return false;
        return p.val === q.val && check(p.left, q.right) && check(p.right, q.left);
    }
    return check(root, root);
};
```

### 二叉树的中序遍历

[题目内容](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

#### 解题思路   

递 🐢。   

#### 代码实现

```ts
/**
 * Definition for a binary tree node.
 * class TreeNode {
 *     val: number
 *     left: TreeNode | null
 *     right: TreeNode | null
 *     constructor(val?: number, left?: TreeNode | null, right?: TreeNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.left = (left===undefined ? null : left)
 *         this.right = (right===undefined ? null : right)
 *     }
 * }
 */

const inorderTraversal = (root: TreeNode | null): number[] => {
    let res: number[] = [];
    const preOrder = (root: TreeNode | null): void => {
        if (!root) return;
        preOrder(root?.left);
        res.push(root?.val);
        preOrder(root?.right);
    } 
    preOrder(root);
    return res;
};
```

### 合并两个有序链表

[题目内容](https://leetcode.cn/problems/merge-two-sorted-lists/)

#### 解题思路

创建新节点。     

#### 代码实现

```ts
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

const mergeTwoLists = (list1: ListNode | null, list2: ListNode | null): ListNode | null => {
    const head: ListNode = new ListNode(-1);
    let current: ListNode = head;
    while(list1 !== null && list2 !== null) {
        if (list1.val < list2.val) {
            current.next = list1;
            list1 = list1.next;
        } else {
            current.next = list2;
            list2 = list2.next
        }
        current = current.next
    }
    if (list1 === null) current.next = list2;
    if (list2 === null) current.next = list1;
    return head.next;
};
```

### 有效的括号

[题目内容](https://leetcode.cn/problems/valid-parentheses/)

#### 解题思路   

栈。    

#### 代码实现

```ts
const isValid = (s: string): boolean => {
    type KeyType = ')' | '}' | ']';
    type ValueType = '(' | '{' | '[';
    const n: number = s.length;
    if (n % 2 === 1) return false;
    const mapObj: Record<KeyType, ValueType> = {
        ')': '(',
        '}': '{',
        ']': '['
    }
    const stk: ValueType[] = [];
    for (let item of s) {
        if (item in mapObj) {
           let top = stk.pop();
           if (mapObj[item] !== top) {
               return false;
           } 
        } else {
            stk.push(item as ValueType);
        }
    }
    return !stk.length;
};
```


### 无重复字符的最长子串

[题目内容](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

#### 解题思路   

字符串转换为数组应用累加器求值。    

#### 代码实现

```ts
const lengthOfLongestSubstring = (s: string): number => {
    let max: number = 0;
    if (s.length <= 1) return s.length;
    s.split('').reduce<string>((acc: string, value: string) => {
        const len = acc.indexOf(value);
        if (len === -1) {
            acc += value;
            max = acc.length > max ? acc.length : max;
            return acc;
        } else {
            acc += value;
            return acc.slice(len + 1);
        }
    }, '')
    return max;
};
```

### 两数相加

[题目内容](https://leetcode.cn/problems/add-two-numbers/)  

#### 解题思路

递 🐢。        

#### 代码实现

```ts
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     val: number
 *     next: ListNode | null
 *     constructor(val?: number, next?: ListNode | null) {
 *         this.val = (val===undefined ? 0 : val)
 *         this.next = (next===undefined ? null : next)
 *     }
 * }
 */

const dfs = (l1: ListNode | null, l2: ListNode | null, carry: number = 0): ListNode => {
    if (!l1 && !l2 && carry === 0) {
        return null;
    }
    const value1 = l1?.val ?? 0;
    const value2 = l2?.val ?? 0;
    const sum = value1 + value2 + carry;
    const node = new ListNode(sum % 10);
    node.next = dfs(l1?.next ?? null, l2?.next ?? null, Math.floor(sum / 10));
    return node;
}
const addTwoNumbers = (l1: ListNode | null, l2: ListNode | null): ListNode | null => {
    return dfs(l1, l2, 0);
};
```

### 两数之和

[题目内容](https://leetcode.cn/problems/two-sum/)

#### 解题思路  

哈希表，两数之和变为两数之差。     

#### 代码实现

```ts
const twoSum = (nums: number[], target: number): number[] => {
    const mapObj: Map<number, number> = new Map();
    for (let i = 0; i < nums.length; i++) {
        if (mapObj.has(target - nums[i])) {
            return [mapObj.get(target - nums[i]), i]
        } else {
            mapObj.set(nums[i], i)
        }
    }
};
```
