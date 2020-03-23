### LeetCode链表专项

链表是一种物理存储单元上非连续、非顺序的存储结构，数据元素的逻辑顺序是通过链表中的指针链接次序实现的。

链表中的数据是以节点来表示的，每个节点的构成为两个部分： 一个是存储元素的数据域，另一个是存储下一个节点地址的指针域 。

```c++
struct ListNode {
    int val;
    struct Node *next;
    ListNode(int x) : val(x), next(NULL) {}
};
```

##### 编程技巧

虚拟头节点：当涉及到对链表头结点操作时，可以设置虚拟头节点指向头节点。

```c++
ListNode* vhead = new ListNode(-1);
vhead->next = head;
```

数组模拟链表：可以用于图论，拉链法哈希等

```c++
// head存储链表头，e[]存储节点的值，ne[]存储节点的next指针，idx表示当前用到了哪个节点
int head, e[N], ne[N], idx;

// 初始化
void init() {
    head = -1;
    idx = 0;
}

// 在链表头插入一个数a
void insert(int a) {
    e[idx] = a, ne[idx] = head, head = idx ++ ;
}

// 将头结点删除，需要保证头结点存在
void remove() {
    head = ne[head];
}
```



##### LeetCode 19  

算法 : (快慢指针)

使用两个指针。第一个指针从列表的开头向前移动 n+1 步，而第二个指针将从列表的开头出发。这两个指针被 n个结点分开。通过同时移动两个指针向前来保持这个恒定的间隔，直到第一个指针到达最后一个结点。此时第二个指针将指向从最后一个结点数起的第n个结点。我们重新链接第二个指针所引用的结点的 next 指针指向该结点的下下个结点。

时间复杂度 : O(n)

空间复杂度 : O(1)

```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        auto p = head, q = head;
        while (n--) p = p->next;
        if (!p) return head->next;
        while (p->next) {
            p = p->next;
            q = q->next;
        }
        q->next = q->next->next;
        return head;
    }
};
```

##### LeetCode 21

算法 : (线性合并)

新建虚拟头结点，设置 ans 指针指向虚拟头结点。若当前 l1 指针指向的结点的值 val 比 l2 指针指向的结点的值 val 小，则令 head 的 next 指针指向 l1，且 l1 后移；否则指向 l2，且 l2后移。然后head指针按照上一部设置好的位置后移。循环以上步骤直到 l1或 l2为空。将剩余的 l1或 l2 接到 head 指针后边。

时间复杂度 : O(n)

空间复杂度 : O(1)

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (!l1) return l2;
        if (!l2) return l1;
        auto head = new ListNode(-1);
        auto ans = head;
        while (l1 && l2) {
            if (l1->val < l2->val) {
                head->next = l1;
                l1 = l1->next;
            } else {
                head->next = l2;
                l2 = l2->next;
            }
            head = head->next;
        }
        if (l1) head->next = l1;
        if (l2) head->next = l2;
        return ans->next;
    }
};
```

##### LeetCode 61

算法 : (快慢指针)

k 可能很大，所以我们令 k = k % n，n 是链表长度。创建两个指针p, q，分别指向虚拟头结点，先让 p 向后移动 k 个位置，然后 p 和 q 同时向后移动，直到 p 走到链表最后一个元素。此时 p 指向链表末尾，q 指向分界点。然后我们把链表从分界点处断开，然后把后半段接在前半段前面即可。

时间复杂度 : O(n)

空间复杂度 : O(1)

```c++
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        if (!head) return NULL;
        auto t = head;
        int n = 0;
        while (t) {
            n++;
            t = t->next;
        }
        k %= n;
        if (!k) return head;
        auto p = head, q = head;
        while (k-- && p) p = p->next;
        while (p->next) {
            p = p->next;
            q = q->next;
        }
        p->next = head;
        head = q->next;
        q->next = NULL;
        return head;
    }
};
```

##### LeetCode 83

算法 : (线性扫描)

时间复杂度 : O(n)

从前往后扫描整个链表，如果一个节点和其后继节点相同，则直接删除后继节点，否则指针移动到后继节点。

空间复杂度 : O(1)

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head) return NULL;
        auto p = head;
        while (p && p->next) {
            if (p->val == p->next->val) {
                p->next = p->next->next;
            }
            else p = p->next;
        }
        return head;
    }
};
```

##### LeetCode 92

算法 : (模拟)

第一步，我们先将 m 到 n 之间的指针翻转，我们将 m 的指针指向 c，将 a 的指针指向 n。

时间复杂度 : O(n)

空间复杂度 : O(1)

```c++
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int m, int n) {
        if (m == n) return head;
        auto vhead = new ListNode(-1);
        vhead->next = head;
        auto p = vhead;
        for (int i = 0; i < m - 1; i ++ ) p = p->next;
        auto a = p, b = a->next, c = b->next;
        for (int i = m + 1; i <= n; i ++ ) {
            auto d = c->next;
            c->next = b;
            b = c;
            c = d;
        }
        a->next->next = c;
        a->next = b;
        return vhead->next;
    }
};
```

##### LeetCode 141

算法 : (快慢指针)

用两个指针从头开始扫描，第一个指针每次走一步，第二个指针每次走两步。如果走到 null，说明不存在环；否则如果两个指针相遇，则说明存在环。假设链表存在环，则当第一个指针走到环入口时，第二个指针已经走到环上的某个位置，距离环入口还差 x 步。由于第二个指针每次比第一个指针多走一步，所以第一个指针再走 x步，两个指针就相遇了。

时间复杂度 : O(n)

空间复杂度 : O(1)

```c++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if (!head || !head->next) return false;
        auto first = head, second = first->next;
        while (first && second) {
            if (first == second) return true;
            first = first->next;
            second = second->next;
            if (second) second = second->next;
        }

        return false;
    }
};
```

##### LeetCode 142

算法 : (快慢指针)

用两个指针 first,second 分别从起点开始走，first 每次走一步，second 每次走两步。如果过程中 second 走到null，则说明不存在环。否则当 first 和 second 相遇后，让 first 返回起点，second待在原地不动，然后两个指针每次分别走一步，当相遇时，相遇点就是环的入口。

证明 : a 是起点，b 是环的入口，c 是两个指针的第一次相遇点，ab 之间的距离是 x，bc 之间的距离是 y。
则当 first 走到 b 时，由于 second 比 first 多走一倍的路，所以 second 已经从 b 开始在环上走了 x 步，可能多余1圈，距离 b 还差 y 步（这是因为第一次相遇点在 b 之后 y 步，我们让 first 退回 b 点，则 second 会退 2y 步，也就是距离 b 点还差 y 步）；所以 second 从 b 点走 x+y 步即可回到 b 点，所以 second 从 c 点开始走，走 x 步即可恰好走到 b 点，同时让 first 从头开始走，走 x 步也恰好可以走到 b 点。所以第二次相遇点就是 b 点。

时间复杂度 : O(n)

空间复杂度 : O(1)

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if (!head || !head->next) return NULL;
        auto first = head, second = head;
        while (first && second) {
            first = first->next;
            second = second->next;
            if (first) first = first->next;
            else return NULL;
            if (first == second) {
                first = head;
                while (first != second) {
                    first = first->next;
                    second = second->next;
                }
                return first;
            }     
        }
        return NULL;
    }
};
```

##### LeetCode 147

算法 : (插入排序)

与插入排序思想一致，建立一个已排序区链表，扫描待排序区链表，对于每个节点在已排序区找到其相应位置插入。

时间复杂度 : O(n<sup>2</sup>)

空间复杂度 : O(1)

```c++
class Solution {
public:
    ListNode* insertionSortList(ListNode* head) {
        if (!head) return NULL;
        auto vh = new ListNode(-1);
        while (head) {
            auto p = vh, t = head->next;
            while (p->next && head->val > p->next->val) {
                p = p->next;
            }
            head->next = p->next;
            p->next = head;
            head = t;
        }
        return vh->next;
    }
};
```

##### LeetCode 160

算法 : (指针扫描)

用两个指针分别从两个链表头部开始扫描，每次分别走一步；如果指针走到null，则从另一个链表头部开始走；
当两个指针相同时，如果指针不是null，则指针位置就是相遇点；如果指针是 null，则两个链表不相交；

时间复杂度 : O(n)

空间复杂度 : O(1)

```c++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        auto a = headA, b = headB;
        while (a != b) {
            if (!a) a = headB;
            else a = a->next;
            if (!b) b = headA;
            else b = b->next;  
        }
        return a;
    }
};
```

##### LeetCode 206

算法 : (链表操作)

建立虚拟头节点连接head，扫描链表，把每一个节点插入虚拟头节点的后面。

时间复杂度 : O(n)

空间复杂度 : O(1)

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (!head) return NULL;
        auto vh = new ListNode(-1);
        vh->next = head;
        while (head->next) {
            auto p = head->next;
            head->next = head->next->next;
            p->next = vh->next;
            vh->next = p;
        }
        return vh->next;
    }
};
```

