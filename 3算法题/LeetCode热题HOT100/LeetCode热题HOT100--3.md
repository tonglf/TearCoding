# LeetCode 热题 HOT 100 -- 3

## 102.二叉树的层序遍历

给你一个二叉树，请你返回其按 **层序遍历** 得到的节点值。 （即逐层地，从左到右访问所有节点）。

 **题解：队列**

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) 
    {
        vector<vector<int>> result;
        if (root == nullptr)
            return result;

        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty())
        {
            int size = q.size();
            vector<int> path;
            for (int i = 0; i < size; ++i)
            {
                TreeNode* cur = q.front();
                q.pop();
                path.push_back(cur->val);
                if (cur->left)
                {
                    q.push(cur->left);
                }
                if (cur->right)
                {
                    q.push(cur->right);
                }
            }
            result.push_back(path);
        }
        return result;
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O*(*n)**

## 104.二叉树的最大深度

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

**题解一：递归**

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) 
    {
        if (root == nullptr)
        {
            return 0;
        }
        int left = maxDepth(root->left);
        int right = maxDepth(root->right);

        return max(left, right) + 1;
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O*(*height)**

**题解二：层次遍历（队列）**

```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) 
    {
        if (root == NULL) return 0;
        int depth = 0;
        queue<TreeNode*> que;
        que.push(root);
        while(!que.empty()) 
        {
            int size = que.size();
            depth++; // 记录深度
            for (int i = 0; i < size; i++) 
            {
                TreeNode* node = que.front();
                que.pop();
                if (node->left) 
                    que.push(node->left);
                if (node->right) 
                    que.push(node->right);
            }
        }
        return depth;
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O*(*n)**

## 105.从前序与中序遍历序列构造二叉树

根据一棵树的前序遍历与中序遍历构造二叉树。

**题解：递归**

```cpp
class Solution {
    using Itor = vector<int>::iterator;
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) 
    {
        return buildTree(preorder.begin(), preorder.end(), inorder.begin(), inorder.end());
    }

    TreeNode* buildTree(Itor preFirst, Itor preLast, Itor inFirst, Itor inLast)
    {
        if (preFirst >= preLast || inFirst >= inLast)
            return nullptr;

        TreeNode* root = new TreeNode(*preFirst);
        Itor index = find(inFirst, inLast, *preFirst);
        int dis = distance(inFirst, index);
        
        root->left = buildTree(preFirst + 1, preFirst + dis + 1, inFirst, index);
        root->right = buildTree(preFirst + dis + 1, preLast, index + 1, inLast);

        return root;
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O*(*n)**

## 114.二叉树展开为链表

给你二叉树的根结点 root ，请你将它展开为一个单链表：

展开后的单链表应该同样使用 TreeNode ，其中 right 子指针指向链表中下一个结点，而左子指针始终为 null 。
展开后的单链表应该与二叉树 先序遍历 顺序相同。

示例 1：

<img src="./Image/114_leetcode.jpg" alt="img" style="zoom:50%;" />

```cpp
输入：root = [1,2,5,3,4,null,6]
输出：[1,null,2,null,3,null,4,null,5,null,6]
```


示例 2：

```cpp
输入：root = []
输出：[]
```


示例 3：

```cpp
输入：root = [0]
输出：[0]
```

**题解：寻找前驱节点**

```cpp
class Solution {
public:
    void flatten(TreeNode* root) 
    {
        TreeNode *curr = root;
        while (curr != nullptr) 
        {
            if (curr->left != nullptr) 
            {
                TreeNode *next = curr->left;
                TreeNode *predecessor = next;
                while (predecessor->right != nullptr) 
                {
                    predecessor = predecessor->right;
                }
                predecessor->right = curr->right;
                curr->left = nullptr;
                curr->right = next;
            }
            curr = curr->right;
        }
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O*(*1)**


## 121.买卖股票的最佳时机

给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。

你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

示例 1：

```cpp
输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```


示例 2：

```c++
输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 没有交易完成, 所以最大利润为 0。
```

**题解一：贪心**

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int ans = 0;
        int minVal = prices[0];
        for (int i = 1; i < prices.size(); ++i)
        {
            if (minVal > prices[i])
            {
                minVal = prices[i];
            }
            ans = max(prices[i] - minVal, ans);
        }
        return ans;
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O*(*1)**

**题解二：动态规划**

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) 
    {
        vector<vector<int>> dp(prices.size(), vector<int>(2));
        dp[0][0] = -prices[0];
        dp[0][1] = 0;

        for (int i = 1; i < prices.size(); ++i)
        {
            dp[i][0] = max(dp[i - 1][0], -prices[i]);
            dp[i][1] = max(dp[i - 1][1], prices[i] + dp[i - 1][0]);
        }

        return dp[prices.size() - 1][1];
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O*(*n)**

## 124.二叉树中的最大路径和

路径 被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。同一个节点在一条路径序列中至多出现一次。该路径 至少包含一个 节点，且不一定经过根节点。

路径和 是路径中各节点值的总和。

给你一个二叉树的根节点 root ，返回其 最大路径和 。

示例 1：

![img](./Image/124_leetcode_1.jpg)

```c++
输入：root = [1,2,3]
输出：6
解释：最优路径是 2 -> 1 -> 3 ，路径和为 2 + 1 + 3 = 6
```

示例 2：

![img](./Image/124_leetcode_2.jpg)

```c++
输入：root = [-10,9,20,null,null,15,7]
输出：42
解释：最优路径是 15 -> 20 -> 7 ，路径和为 15 + 20 + 7 = 42
```

**题解：递归**

```cpp
class Solution {
private:
    int maxSum = INT_MIN;
public:
    int maxGain(TreeNode* node) 
    {
        if (node == nullptr) 
        {
            return 0;
        }
        
        // 递归计算左右子节点的最大贡献值
        // 只有在最大贡献值大于 0 时，才会选取对应子节点
        int leftGain = max(maxGain(node->left), 0);
        int rightGain = max(maxGain(node->right), 0);

        // 节点的最大路径和取决于该节点的值与该节点的左右子节点的最大贡献值
        int priceNewpath = node->val + leftGain + rightGain;

        // 更新答案
        maxSum = max(maxSum, priceNewpath);

        // 返回节点的最大贡献值
        return node->val + max(leftGain, rightGain);
    }

    int maxPathSum(TreeNode* root) 
    {
        maxGain(root);
        return maxSum;
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O*(*n)**


## 128.最长连续序列

给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

进阶：你可以设计并实现时间复杂度为 O(n) 的解决方案吗？

示例 1：

```c++
输入：nums = [100,4,200,1,3,2]
输出：4
解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

示例 2：

```cpp
输入：nums = [0,3,7,2,5,8,4,6,0,1]
输出：9
```

**题解：哈希表**

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) 
    {
        unordered_set<int> num_set;
        for (const int& num : nums) 
        {
            num_set.insert(num);
        }

        int longestStreak = 0;

        for (const int& num : num_set) 
        {
            if (!num_set.count(num - 1)) 
            {
                int currentNum = num;
                int currentStreak = 1;

                while (num_set.count(currentNum + 1)) 
                {
                    currentNum += 1;
                    currentStreak += 1;
                }

                longestStreak = max(longestStreak, currentStreak);
            }
        }
        return longestStreak;           
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O*(*n)**

## 136.只出现一次的数字

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

说明：

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

示例 1:

```cpp
输入: [2,2,1]
输出: 1
```


示例 2:

```cpp
输入: [4,1,2,1,2]
输出: 4
```

**题解：位运算**

```cpp
int singleNumber(vector<int>& nums)
{
    int ret = 0;
    for (auto e : nums)
        ret ^= e;
    return ret;
}
```

- **时间复杂度：O(N)**

- **空间复杂度：O(1)**

## 139.单词拆分

给定一个非空字符串 s 和一个包含非空单词的列表 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

说明：	 拆分时可以重复使用字典中的单词。
				你可以假设字典中没有重复的单词。
示例 1：

```cpp
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。
```


示例 2：

```c++
输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以被拆分成 "apple pen apple"。
     注意你可以重复使用字典中的单词。
```


示例 3：

```c++
输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false
```

**题解：动态规划——完全背包**

```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) 
    {
        unordered_set<string> wordSet(wordDict.begin(), wordDict.end());
        vector<bool> dp(s.size() + 1, false);
        dp[0] = true;
        for (int i = 1; i <= s.size(); i++) 	// 遍历背包
        {   
            for (int j = 0; j < i; j++) 		// 遍历物品
            {       
                string word = s.substr(j, i - j); // substr (起始位置，截取的个数)
                if (wordSet.find(word) != wordSet.end() && dp[j]) 
                {
                    dp[i] = true;
                }
            }
        }
        return dp[s.size()];
    }
};
```

**时间复杂度：O(n^2)**

**空间复杂度：O*(*n)**

## 141.环形链表

给定一个链表，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。

如果链表中存在环，则返回 true 。 否则，返回 false 。

进阶：

你能用 O(1)（即，常量）内存解决此问题吗？

示例 1：

![img](./Image/141_leetcode_1.png)

```cpp
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```


示例 2：

![img](./Image/141_leetcode_2.png)

```cpp
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```


示例 3：

![img](./Image/141_leetcode_3.png)

```cpp
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

**题解一：快慢指针**

```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) 
    {
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast && fast->next)
        {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast)
            {
                return true;
            }
        }
        return false;
    }
};
```

- **时间复杂度：O(N)**

- **空间复杂度：O(1)**

**题解二：哈希表**

```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) 
    {
        unordered_set<ListNode*> seen;
        while (head != nullptr) 
        {
            if (seen.count(head)) 
            {
                return true;
            }
            seen.insert(head);
            head = head->next;
        }
        return false;
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O*(*n)**

## 142.环形链表 II

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。注意，pos 仅仅是用于标识环的情况，并不会作为参数传递到函数中。

说明：不允许修改给定的链表。

进阶：

你是否可以使用 O(1) 空间解决此题？

示例 1：

```cpp
输入：head = [3,2,0,-4], pos = 1
输出：返回索引为 1 的链表节点
解释：链表中有一个环，其尾部连接到第二个节点。
```


示例 2：

```cpp
输入：head = [1,2], pos = 0
输出：返回索引为 0 的链表节点
解释：链表中有一个环，其尾部连接到第一个节点。
```


示例 3：

```cpp
输入：head = [1], pos = -1
输出：返回 null
解释：链表中没有环。
```

**题解一：快慢指针**

```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) 
    {
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast && fast->next)
        {
            slow = slow->next;
            fast = fast->next->next;
            if (slow == fast)
            {
                ListNode* cur = head;
                while (cur != fast)
                {
                    cur = cur->next;
                    fast = fast->next;
                }
                return cur;
            }
        }
        return nullptr;
    }
};
```

- **时间复杂度：O(N)**

- **空间复杂度：O(1)**

**题解二：哈希表**

```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) 
    {
        unordered_set<ListNode*> seen;
        while (head != nullptr) 
        {
            if (seen.count(head)) 
            {
                return head;
            }
            seen.insert(head);
            head = head->next;
        }
        return nullptr;
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O*(*n)**

## 146.LRU  缓存机制

运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制 。
实现 LRUCache 类：

LRUCache(int capacity) 以正整数作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。


进阶：你是否可以在 O(1) 时间复杂度内完成这两种操作？

示例：

```cpp
输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
```

**题解：哈希表 + 双向链表**

```cpp
#include <iostream>
#include <list>
#include <unordered_map>

using namespace std;

class LRUCache
{
private:
	struct CacheNode
	{
		int key;
		int value;
		CacheNode(int k, int v) : key(k), value(v) { }
	};

public:
	LRUCache(int capacity) : m_capacity(capacity) { }

	int get(int key)
	{
		if (m_cacheMap.find(key) == m_cacheMap.end())
		{
			return -1;
		}

		// 把当前访问的节点移到链表的头部，并且更新 map 中该节点的地址
        // 把 m_cacheList 中 key 对应的迭代器放置到 m_cacheList.begin() 位置上
		m_cacheList.splice(m_cacheList.begin(), m_cacheList, m_cacheMap[key]); 
		m_cacheMap[key] = m_cacheList.begin();
		return m_cacheMap[key]->value;
	}

	void put(int key, int value)
	{
		if (m_cacheMap.find(key) == m_cacheMap.end())
		{
			if (m_cacheList.size() == m_capacity)
			{
				m_cacheMap.erase(m_cacheList.back().key);
				m_cacheList.pop_back();
			}
			// 插入新节点到链表头部，并且在 map 中增加该节点
			m_cacheList.push_front(CacheNode(key, value));
			m_cacheMap[key] = m_cacheList.begin();
		}
		else
		{
			// 更新节点的值，把当前访问的节点移到链表头部，并且更新 map 中该节点的地址
			m_cacheMap[key]->value = value;
			m_cacheList.splice(m_cacheList.begin(), m_cacheList, m_cacheMap[key]);
			m_cacheMap[key] = m_cacheList.begin();
		}
	}

private:
	list<CacheNode> m_cacheList;
	unordered_map<int, list<CacheNode>::iterator> m_cacheMap;
	int m_capacity;
};
```

**时间复杂度：O*(*1)**

**空间复杂度：O*(*1)**

## 148.排序链表

给你链表的头结点 head ，请将其按 升序 排列并返回 排序后的链表 。

进阶：

你可以在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序吗？

示例 1：

```cpp
输入：head = [4,2,1,3]
输出：[1,2,3,4]
```


示例 2：

```cpp
输入：head = [-1,5,3,4,0]
输出：[-1,0,3,4,5]
```

示例 3：

```c++
输入：head = []
输出：[]
```

**题解：自底向上归并排序**

```cpp
class Solution {
public:
    ListNode* sortList(ListNode* head) 
    {
        if (head == nullptr) 
        {
            return head;
        }
        int length = 0;
        ListNode* node = head;
        while (node != nullptr) 
        {
            length++;
            node = node->next;
        }
        ListNode* dummyHead = new ListNode(0, head);
        for (int subLength = 1; subLength < length; subLength <<= 1) 
        {
            ListNode* prev = dummyHead, *curr = dummyHead->next;
            while (curr != nullptr) 
            {
                ListNode* head1 = curr;
                for (int i = 1; i < subLength && curr->next != nullptr; i++) 
                {
                    curr = curr->next;
                }
                ListNode* head2 = curr->next;
                curr->next = nullptr;
                curr = head2;
                for (int i = 1; i < subLength && curr != nullptr && curr->next != nullptr; i++) 
                {
                    curr = curr->next;
                }
                ListNode* next = nullptr;
                if (curr != nullptr) 
                {
                    next = curr->next;
                    curr->next = nullptr;
                }
                ListNode* merged = merge(head1, head2);
                prev->next = merged;
                while (prev->next != nullptr) 
                {
                    prev = prev->next;
                }
                curr = next;
            }
        }
        return dummyHead->next;
    }

    ListNode* merge(ListNode* head1, ListNode* head2) 
    {
        ListNode* dummyHead = new ListNode(0);
        ListNode* temp = dummyHead, *temp1 = head1, *temp2 = head2;
        while (temp1 != nullptr && temp2 != nullptr) 
        {
            if (temp1->val <= temp2->val) 
            {
                temp->next = temp1;
                temp1 = temp1->next;
            } 
            else 
            {
                temp->next = temp2;
                temp2 = temp2->next;
            }
            temp = temp->next;
        }
        if (temp1 != nullptr) 
        {
            temp->next = temp1;
        } 
        else if (temp2 != nullptr) 
        {
            temp->next = temp2;
        }
        return dummyHead->next;
    }
};
```

**时间复杂度：O*(*n*log*n)**

**空间复杂度：O(1)**

## 152.乘积最大数组

给你一个整数数组 nums ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

示例 1:

```cpp
输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```

示例 2:

```cpp
输入: [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。
```

**题解：动态规划**

```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) 
    {
        vector <int> maxF(nums), minF(nums);
        for (int i = 1; i < nums.size(); ++i) 
        {
            maxF[i] = max(maxF[i - 1] * nums[i], max(nums[i], minF[i - 1] * nums[i]));
            minF[i] = min(minF[i - 1] * nums[i], min(nums[i], maxF[i - 1] * nums[i]));
        }
        return *max_element(maxF.begin(), maxF.end());
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O(1)**


## 155.最小栈

设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

push(x) —— 将元素 x 推入栈中。
pop() —— 删除栈顶的元素。
top() —— 获取栈顶元素。
getMin() —— 检索栈中的最小元素。

示例:

```c++
输入：
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

输出：
[null,null,null,null,-3,null,0,-2]

解释：
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
```

**题解：辅助栈**

```cpp
class MinStack {
public:
    MinStack() { }
    
    void push(int val) 
    {
        if (dataS.empty())
        {
            dataS.push(val);
            minS.push(val);
        }
        else
        {
            dataS.push(val);
            int minVal = min(val, minS.top());
            minS.push(minVal);
        }
    }
    
    void pop() 
    {
        dataS.pop();
        minS.pop();
    }
    
    int top() 
    {
        return dataS.top();
    }
    
    int getMin() {
        return minS.top();
    }

private:
    stack<int> dataS;		// 保存真实数据
    stack<int> minS;		// 保存最小值
};
```

**时间复杂度：O*(*1)**

**空间复杂度：O(n)**

## 160.相交链表

给你两个单链表的头节点 headA 和 headB ，请你找出并返回两个单链表相交的起始节点。如果两个链表没有交点，返回 null 。

图示两个链表在节点 c1 开始相交：

题目数据 保证 整个链式结构中不存在环。

注意，函数返回结果后，链表必须 保持其原始结构 。

 ![img](./Image/160_leetcode_1.png)

示例 1：

```cpp
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Intersected at '8'
解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。
在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```


示例 2：

```cpp
输入：intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
输出：Intersected at '2'
解释：相交节点的值为 2 （注意，如果两个链表相交则不能为 0）。
从各自的表头开始算起，链表 A 为 [0,9,1,2,4]，链表 B 为 [3,2,4]。
在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。
```


示例 3：

```cpp
输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。
由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
这两个链表不相交，因此返回 null 。
```

**题解：双指针**

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (headA == nullptr || headB == nullptr) {
            return nullptr;
        }
        ListNode *pA = headA, *pB = headB;
        while (pA != pB) {
            pA = pA == nullptr ? headB : pA->next;
            pB = pB == nullptr ? headA : pB->next;
        }
        return pA;
    }
};
```

**时间复杂度：O*(*m + n)**

**空间复杂度：O(1)**

## 198.打家劫舍

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

示例 1：

```cpp
输入：[1,2,3,1]
输出：4
解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
```

示例 2：

```cpp
输入：[2,7,9,3,1]
输出：12
解释：偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。
```

**题解：动态规划**

```cpp
class Solution {
public:
    int rob(vector<int>& nums) 
    {
        if (nums.size() <= 2)
        {
            return *max_element(nums.begin(), nums.end());
        }
        vector<int> dp(nums.size(), 0);
        dp[0] = nums[0];
        dp[1] = max(nums[0], nums[1]);
        for (int i = 2; i < nums.size(); ++i)
        {
            dp[i] = max(dp[i - 1], dp[i - 2] + nums[i]);
        }
        return dp[nums.size() - 1];
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O(n)**

## 200.岛屿数量

[链接](https://leetcode-cn.com/problems/number-of-islands/)

给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

示例 1：

```cpp
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```

示例 2：

```cpp
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```

**题解：深度优先搜索**

我们可以将二维网格看成一个无向图，竖直或水平相邻的 11 之间有边相连。

为了求出岛屿的数量，我们可以扫描整个二维网格。如果一个位置为 1，则以其为起始节点开始进行深度优先搜索。在深度优先搜索的过程中，每个搜索到的 1 都会被重新标记为 0。

最终岛屿的数量就是我们进行深度优先搜索的次数。

```cpp
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) 
    {
        int count = 0;
        int r = grid.size();
        int c = grid[0].size();

        for (int i = 0; i < r; ++i)
        {
            for (int j = 0; j < c; ++j)
            {
                if (grid[i][j] == '1')
                {
                    count++;
                    dfs(grid, i, j);
                }
            }
        }
        return count;
    }

    void dfs(vector<vector<char>>& grid, int i, int j)
    {
        int r = grid.size();
        int c = grid[0].size();
        grid[i][j] = '0';

        if (i + 1 < r && grid[i + 1][j] == '1')     dfs(grid, i + 1, j);
        if (i - 1 >= 0 && grid[i - 1][j] == '1')    dfs(grid, i - 1, j);
        if (j + 1 < c && grid[i][j + 1] == '1')     dfs(grid, i, j + 1);
        if (j - 1 >= 0 && grid[i][j - 1] == '1')    dfs(grid, i, j - 1);
    }
};
```

**时间复杂度：O(MN)**

**空间复杂度：O(MN)**

## 206.反转链表

给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。

示例 1：

```cpp
输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]
```


示例 2：

```cpp
输入：head = [1,2]
输出：[2,1]
```

示例 3：

```cpp
输入：head = []
输出：[]
```

**题解：迭代**

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) 
    {
        if (head == nullptr)
        {
            return head;
        }
        ListNode* cur = head;
        ListNode* pre = nullptr;
        ListNode* next = nullptr;
        while (cur)
        {
            next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
};
```

**时间复杂度：O*(*n)**

**空间复杂度：O(1)**
