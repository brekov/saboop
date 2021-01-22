# 数字求和
## 问题描述
Given an array of integers, return **indices** of the two numbers such that they add up to a specific target.

You may assume that each input would have **exactly** one solution, and you may not use the *same* element twice.

Example:

```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1]. 
```

## 问题分析
从数组中找到符合“求和等于指定值”这一条件的两个数字，给出下标。假定背景条件，每个输入都只有一个解，并且同一个数字不能使用两次。

## 解决方案

### 硬遍历

* **解决思路**

  针对数组中的每一个值`x`，遍历数组，确认是否存在值`y`，满足`x+y=target`。

* **代码实现**
    ```java
    public int[] twoSum(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            for (int j = i + 1; j < nums.length; j++) {
                if (nums[j] == target - nums[i]) {
                    return new int[] { i, j };
                }
            }
        }
        throw new IllegalArgumentException("No two sum solution");
    }
    ```

* **复杂度分析**
    * 时间复杂度：O(n^2). 对于每个元素都要遍历后续的元素，所以复杂度是O(n^2).
    * 控件复杂度：O(1). 