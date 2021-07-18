

枚举两个坐标这种题目，想一下两个数的顺序有没有关系？？？
如果顺序没有关系，比如num[i] + nums[j] 跟nums[j] +nums[i]
我们可以加个条件： j < i 这样不影响答案

假设只有一种solution，或者只用返回任意一组解
相同的元素(下标相同)不能重复使用，数组中可能有重复的值，如果下标不同，看做不同的元素





### 朴素解法
- 由于我们每次要从数组中找两个数。

* 因此一个很简单的思路是：使用两重循环枚举下标 i 和 j ，分别代表要找的两个数。

* 然后判断 $nums[i] + nums[j] == target$ 是否成立。

* 另外为了防止得到重复的解，我们需要在第一层循环中让 i 从 0 开始，到 n - 2 结束（确保能取到下一位数作为 j ）；在第二层循环中让 j 从 i + 1 开始，到 n - 1 结束。
```java
class Solution {
    public int[] twoSum(int[] nums, int t) {
        int n = nums.length;
        for (int i = 0; i < n - 1; i++) {
            for (int j = i + 1; j < n; j++) {
                if (t == nums[i] + nums[j]) return new int[]{i,j};
            }
        }
        return new int[]{};
    }
}
```
* 时间复杂度：两重循环，时间复杂度(n^2) 空间复杂度O(1)


method 1: 哈希
哈希表解法
* 首先，任何优化的思路都不外乎「减少重复」。

* 从朴素解法中可以知道，逻辑上我们是先定下来一个数，然后从数组中往后找另外一个值是否存在。但其实我们在找第二个数的过程中是重复扫描了数组多次。

* 举个例子，对于 nums = [2,3,8,4], target = 9 的样例，我们先确定下来第一个数是 2，然后从后扫描到最后一个数，检查是否有 7。发现没有，再决策第一个数为 3 的情况，这时候我们应该利用前一次扫描的结果来帮助我们快速判断是否存在 6，而不是再重新进行一次扫描。

* 这是直观的优化思路，不难想到可以使用哈希表进行存储。以数值为 key，数值的下标为 value。

* 当动手将想法转化为代码时，会发现如果先敲定第一个数，将后面的数加入哈希表，再进行下一位的遍历的时候，还需要将该数值从哈希表中进行删除。

```
class Solution {
    public int[] twoSum(int[] nums, int t) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) map.put(nums[i], i);
        for (int i = 0; i < nums.length; i++) {
            int a = nums[i], b = t - a;
            if (map.get(a) == i) map.remove(a);
            if (map.containsKey(b)) return new int[]{i, map.get(b)};
        }
        return new int[]{};
    }
}

* ```
最坏情况下，每个数会对应一次哈希表的插入和删除。该解法本质是在循环过程中敲定第一个数，在哈希表中找该数后面是否存在第二个数。

这时候不妨将思路转换过来，遍历过程中敲定第二个数，使用哈希表在第二个数的前面去找第一个数。

具体的做法是，边遍历边存入哈希表，遍历过程中使用的下标 i 用作敲定第二个数，再从现有的哈希表中去找另外一个目标数（由于下标 i 前面的数都被加入哈希表了，即在下标 i 前面去找第一个数）。

```
    public int[] twoSum(int[] nums, int target) {
        // sanity check
        if (nums == null || nums.length == 0) return new int[]{-1, -1};
        Map<Integer, Integer> visited = new HashMap<>();
        for (int i = 0; i <nums.length; i++) {
            if (visited.containsKey(target - nums[i])) {
                return new int[]{visited.get(target - nums[i]), i};
            }
            visited.put(nums[i], i);
        }
        return new int[]{-1, -1};
    }

```
从 LeetCode 上的执行时间来看，第一种哈希表做法是 4ms，而第二种哈希表的做法是 0ms（不足 1ms 的意思），快在了我们减少了哈希表的插入和删除操作。

但这只是常数级别上的优化，LeetCode 上的执行时间建议只作普通参考，还是要从算法的时空复杂度来分析快慢。

时间复杂度：第一种哈希表的做法会扫描数组两次，复杂度是 $O(n)$（忽略常数）；第二种做法只会对数组进行一遍扫描，复杂度是 $O(n)$ * 空间复杂度：两种做法都使用了哈希表进行记录，复杂度是 $O(n)$