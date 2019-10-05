# 冒泡排序
算法系列参考[郭耀华](https://www.cnblogs.com/guoyaohua/p/8600214.html)
>曾经，我不懈看这种题目，觉得好简单，现在...

冒泡排序是一种简单的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果它们的顺序错误就把它们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。

### 1 算法描述
- 比较相邻的元素。如果第一个比第二个大，就交换它们两个；
- 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对，这样在最后的元素应该会是最大的数；
- 针对所有的元素重复以上的步骤，除了最后一个；
- 重复步骤1~3，直到排序完成。
### 2 动图演示
![](https://images2017.cnblogs.com/blog/849589/201710/849589-20171015223238449-2146169197.gif)
### 3 代码实现
改进版较基础版增加了条件判出功能！
> 基础版

```java
class Solution {
    public static int[] bubbleSort(int[] A, int n) {

        for (int i = 0; i < n - 1; i++) {
            for (int j = 0; j < n - i - 1; j++) {
                if (A[j] > A[j + 1]) {
                    int tmp = A[j];
                    A[j] = A[j + 1];
                    A[j + 1] = tmp;
                }
            }
        }
        return A;
    }
}
```
> 优化版
```java
class Solution{
    public static int[] bubbleSort(int[] A, int n) {
        // 冒泡排序执行n - 1 次即可
        for (int i = 0; i < n - 1; i++) {
            boolean finish = true;
            for (int j = 0; j < n - 1 - i; j++) {
                if (A[j] > A[j + 1]) {
                    int tmp = A[j];
                    A[j] = A[j + 1];
                    A[j + 1] = tmp;
                    finish = false;
                }

            }
            // 如果一次也没遍历，说明已经有序
            if (finish)
                break;
        }
        return A;
    }
}
```
### 4 算法分析
最佳情况：T(n) = O(n)  
最差情况：T(n) = O(n^2)  
平均情况：T(n) = O(n^2)