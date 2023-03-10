---
title: "LeetCode Top100 刷题"
date: 2023-03-10T15:10:47+08:00
weight: 3
tags: ["算法"]
categories: ["算法"]
---

🧠 越来越不好使，刷点算法题提高点智商。   

<!--more-->

### 二叉树的中序遍历

[题目内容](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

解题思路：递 🐢。   

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

解题思路：创建新结点。  

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

function mergeTwoLists(list1: ListNode | null, list2: ListNode | null): ListNode | null {
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

解题思路：栈。

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

解题思路：字符串转换为数组应用累加器求值。

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

解题思路：递 🐢。       

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

解题思路：哈希表，两数之和变为两数之差。   

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
