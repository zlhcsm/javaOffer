# 找出B中不属于A的数
> 找出数组B中不属于A的数，数组A有序而数组B无序。假设数组A有n个数，数组B有m个数，写出算法并分析时间复杂度。
## 最直白的解决方案
```java
class Solution {
    // 两次for循环
    public static List<Integer> findDifferent1(int[] A, int[] B) {
        List<Integer> ans = new ArrayList<>();
        for (int i = 0; i < B.length; i++) {
            boolean exit = true;
            for (int j = 0; j < A.length; j++) {
                if (B[i] == A[j]) {
                    exit = false;
                    break;
                }
            }
            if(exit){
                ans.add(B[i]);
            }
        }
        return ans;
    }
}
```
## 优化思路
1， 因为A有序，所以可以使用二分查找  
2， 可以将B排好序，比较