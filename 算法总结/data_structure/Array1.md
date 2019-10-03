### 第k个数问题
[leetcod414. 第三大的数](https://leetcode-cn.com/problems/third-maximum-number/)  
[牛客网 第二大数](https://www.nowcoder.com/questionTerminal/ce710d3a27ca475b97bbae0cb227f1b5)

输入n个整数，查找数组中第二大的数

`输入描述:`
>第一行n表示n个数，第二行n个空格隔开的数


`输出描述:`
>输出第二大的数

`示例1`
>输入  
5  
1 2 3 4 5  
输出  
4  

注意：数字没有重复，其他情况请自行考虑
#### 基础解法
>采用两个变量来解决问题
```java
class Solution{
    private static int SecondMaxNumber(int[] arr) {
        int first  = Integer.MIN_VALUE;
        int second = Integer.MIN_VALUE;

        // 三种情况
        // 1,当数字小于second时，不做处理
        // 2,当数字大于first时，全部后移处理
        // 3,当数字大于second时，second后移处理
        for (int i : arr) {
            if (i > first){
                second = first;
                first = i;
            }
            else if (i > second){
                second = i;
            }
        }
        return second;
    }
}
```
#### 最小堆
>设置一个为k的最小堆，不满则插入，满则比较
```java
class Solution{
     private static int KthMaxNumber(int[] arr, int k) {
        // 如果不符合条件，返回-1
        if (k > arr.length) return -1;

        // 默认最小堆
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        
        // 遍历整个数组
        for (int i : arr) {
            // 补满
            if (minHeap.size() < k)
                minHeap.offer(i);
            // 只选择比对顶大的数字插入
            else if(minHeap.size() >= k && i > minHeap.peek()) {
                minHeap.poll();
                minHeap.offer(i);
            }
        }
        // 弹出对顶元素
        return minHeap.peek();
    }
}
```
