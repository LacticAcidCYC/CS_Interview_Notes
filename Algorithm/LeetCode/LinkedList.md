# <1>Two Pointers Problem (Fast + Slow)

## 1. LeetCode 141 [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

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
    bool hasCycle(ListNode *head) {
        if (!head || !head->next) { return false;}
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast && fast->next) {
            fast = fast->next->next;
            slow = slow->next;
            if (slow == fast) {
                return true;
            }
        }
        return false;
    }
};
```



## 2. LeetCode 142 [Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

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
    ListNode *detectCycle(ListNode *head) {
        if (!head || !head->next) { return NULL;}
        ListNode* fast = head;
        ListNode* slow = head;
        ListNode* p = head;
        while (fast && fast->next) {
            fast = fast->next->next;
            slow = slow->next;
            if (fast == slow) {
                if (p == slow) {
                    return p;
                } else {
                    while (p) {
                        p = p->next;
                        slow = slow->next;
                        if (p == slow) {
                            return p;
                        }
                    }
                }
            }
        }
        return NULL;
    }
};
```



### Related:

### LeetCode 287 [Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)

#### Explanation

Change the Problem to find a cycle in the linkedlist!

e.g. nums=[1,3,4,2,2]

=> nodes = [0=>1, 1=>3, 2=>4, 3=>2, 4=>2]

=> 0->1->3->2->4->2



e.g. [3,1,3,4,2] => 0->3->4->2->3 + 1->1

**Thus, what we need to do is to find the circle and get the first node of the circle, which is the duplicate number. And in this question, the cycle must happen after the first node.**

```c++
// Time Complexity: O(n)
// Space Complexity: O(1)

class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int len = nums.size(); // len should be larger than 1
        if (len < 2) return -1;
        if (len == 2) return nums[0];
        int slow = nums[0], fast = nums[nums[0]];
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[nums[fast]];
        }
        
        int p = 0;
        while (nums[p] != nums[slow]) {
            p = nums[p];
            slow = nums[slow];
        }
        return nums[p];
    }
};
```



## 3. LeetCode 160 [Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)

### (1) Two Pointers

```c++
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
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (!headA || !headB) return NULL;
        
        ListNode* iterA = headA;
        ListNode* iterB = headB;
        
        while (iterA->next && iterB->next) {
            iterA = iterA->next;
            iterB = iterB->next;
        }
        
        int balance = 0;
        if (!iterA->next) {
            while (iterB->next) {
                iterB = iterB->next;
                balance++;
            }
        } else {
            while (iterA->next) {
                iterA = iterA->next;
                balance--;
            }
        }
        
        if (iterA != iterB) return NULL;
        iterA = headA;
        iterB = headB;
        if (balance > 0) {
            while (balance--) {
                iterB = iterB->next;
            }
        } else {
            while (balance++) {
                iterA = iterA->next;
            }
        }
        
        while (iterA != iterB) {
            iterA = iterA->next;
            iterB = iterB->next;
        }
        return iterA;
    }
};
```

[solution](https://leetcode.com/articles/intersection-of-two-linked-lists/)



































