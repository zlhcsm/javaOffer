# 快速排序（QuickSort)
这里应该好好看！要不然还是不会。。
## 递归
```java
import java.util.*;

public class QuickSort {
    public static int[] quickSort(int[] A, int n) {
        // write code here
        if (A == null || A.length < 2)
            return null;

        sort(A, 0, n - 1);
        return A;
    }

    public static void sort(int[] A, int lo, int hi) {
        if (lo < hi) {
            int mid = partition(A, lo, hi);
            sort(A, 0, mid - 1);
            sort(A, mid + 1, hi);
        }
    }

    public static int partition(int[] A, int lo, int hi) {
        if (lo == hi)
            return lo;
        int index = lo;
        for (int i = lo; i < hi; i++) {
            // 前边的小于等于比较数，放在前边
            if (A[i] <= A[hi]) {
                swap(A,i , index);
                index++;
            }
        }
        swap(A, index, hi);
        return index;
    }

    public static void swap(int[] arr, int la, int lb) {
        int tmp = arr[lb];
        arr[lb] = arr[la];
        arr[la] = tmp;
    }
}

```
## 非递归
```java
import java.util.*;
// 非递归
public class QuickSort {
    // 调用主体
    public static int[] quickSort(int[] A, int n) {
        // write code here
        if (A == null || A.length < 2)
            return null;
        sort(A, 0, n - 1);
        return A;
    }
    // 使用栈来存储递归的角标移动
    public static void sort(int[] A, int lo, int hi) {
        if(lo < hi){
            LinkedList<Integer> stack = new LinkedList<>();
            stack.push(hi);
            stack.push(lo);
            while(!stack.isEmpty()){
                int l = stack.pop();
                int h = stack.pop();
                int mid = partition(A, l, h);
                // 下边这个是为了保证最少有两个元素
                // 加我们压入的是l -> mid - 1，如果此时 l < mid -1 则无意义
                // l = mid - 1时说明只有一个元素，可能 l > mid - 1 么？  怎么可能，mid是中间值啊
                if(l < mid - 1){
                    stack.push(mid - 1);
                    stack.push(l);
                }
                if(h > mid + 1){
                    stack.push(h);
                    stack.push(mid + 1);
                }
            }
        }
    }
    // 排序
    public static int partition(int[] A, int lo, int hi) {
        if (lo == hi)
            return lo;
        int index = lo;
        for (int i = lo; i < hi; i++) {
            // 前边的小于等于比较数，放在前边
            if (A[i] <= A[hi]) {
                swap(A,i , index);
                index++;
            }
        }
        swap(A, index, hi);
        return index;
    }
    // 交换
    public static void swap(int[] arr, int la, int lb) {
        int tmp = arr[lb];
        arr[lb] = arr[la];
        arr[la] = tmp;
    }
}
```