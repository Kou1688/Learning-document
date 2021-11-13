# 剑指offer 09.用两个栈实现队列

**题目**：

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 `appendTail` 和 `deleteHead` ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，`deleteHead` 操作返回 -1 )

**示例**：

```markdown
输入：
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
输出：[null,null,3,-1]
```



**思路：**

首先要知道栈是后进先出，队列是先进先出，那么如果一个栈反转过来就是队列的正常顺序。所以添加用一个栈正常添加，将主栈内的元素依次出栈到辅助栈中，出队时对辅助栈进行正常出栈即可。



**代码：**

```java
/**
 * 用两个栈实现队列2
 *
 * @author KouChaoJie
 * @since: 2021/11/6 14:20
 */
public class CQueue {
    /**
     * 栈1用来添加
     */
    LinkedList<Integer> stack1;
    /**
     * 栈2用来辅助表示队列的出队特性
     */
    LinkedList<Integer> stack2;

    public CQueue() {
        stack1 = new LinkedList<>();
        stack2 = new LinkedList<>();
    }

    /**
     * 在队列尾部插入整数
     */
    public void appendTail(int value) {
        stack1.add(value);
    }

    /**
     * 在队列头部删除整数
     *
     * @return 删除的元素，若为空返回-1
     */
    public int deleteHead() {
        //当辅助栈不为空,删除辅助栈的最后一个元素即可
        if (!stack2.isEmpty()) {
            return stack2.removeLast();
        }
        //若主栈为空则队列内无元素,返回-1
        if (stack1.isEmpty()) {
            return -1;
        }
        //若主栈不为空,将主栈内元素依次出栈添加到辅助栈中,形成队列
        while (!stack1.isEmpty()) {
            stack2.add(stack1.removeLast());
        }
        return stack2.removeLast();
    }
}
```





# 剑指 Offer 04. 二维数组中的查找

**题目：**

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

**示例：**

现有矩阵matrix如下：

```json
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

给定 target = `5`，返回 `true`。

给定 target = `20`，返回 `false`。

**思路：**

遇到有序数组，首先应该想到二分查找。

```java
/**
 * @author KouChaoJie
 * @since: 2021/11/7 14:26
 */
public class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        boolean isTarget = false;
        for (int[] arr : matrix) {
            int left = 0, right = arr.length - 1;
            while (left <= right) {
                int mid = (left + right) / 2;
                if (arr[mid] > target) {
                    right = mid - 1;
                } else if (arr[mid] < target) {
                    left = mid + 1;
                } else {
                    isTarget = true;
                    break;
                }
            }
        }

        return isTarget;
    }
}
```
