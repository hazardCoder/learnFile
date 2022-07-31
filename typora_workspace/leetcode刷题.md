# 二进制

## 获取最后一位二进制数

```java
n & 1
```

# 树

## 中序遍历迭代

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<Integer>();
        Deque<TreeNode> stk = new LinkedList<TreeNode>();
        while (root != null || !stk.isEmpty()) {
            // 一直向左推进
            while (root != null) {
                stk.push(root);
                root = root.left;
            }
            root = stk.pop();
            res.add(root.val);
            // 向右试探
            root = root.right;
        }
        return res;
    }
}
```

# 数组/哈希

## 原地哈希

将元素放到同样的元素索引下，比如把3放到索引为3的位置，目的是**以索引的存在来标识元素的存在**，比如索引2的位置标志为真，那么说明在原来的数组存在元素2，所以当我们想知道元素的存在就可以转换为索引的存在，查找复杂度从O(n)转换为O(1)

![image-20220429210947025](D:\tools\typora\Typora-pic\image-20220429210947025.png)

图片来源于[leetcode 41题](https://leetcode-cn.com/problems/first-missing-positive/)，重点在最后两行

如果按照正常思想，从左往右遍历，【3放到索引2，4放到索引3】，此时轮到1时，就不知道原来索引为3的地方有个1，覆盖了信息

所以图中展示了一种方式，**将元素转化为对应值的负数**，那么在中途可以通过取绝对值的方式查看覆盖前的信息，相当于此时保存了两类信息，**转换前**和**转换后**的数据

