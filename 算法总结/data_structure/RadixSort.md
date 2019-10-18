# 基数排序
基数排序也是非比较的排序算法，对每一位进行排序，从最低位开始排序，复杂度为O(kn),为数组长度，k为数组中的数的最大的位数；

基数排序是按照低位先排序，然后收集；再按照高位排序，然后再收集；依次类推，直到最高位。有时候有些属性是有优先级顺序的，先按低优先级排序，再按高优先级排序。最后的次序就是高优先级高的在前，高优先级相同的低优先级高的在前。基数排序基于分别排序，分别收集，所以是稳定的。
## 算法描述
- 取得数组中的最大数，并取得位数；
- arr为原始数组，从最低位开始取每个位组成radix数组；
- 对radix进行计数排序（利用计数排序适用于小范围数的特点）；
## 动图演示
![](https://images2017.cnblogs.com/blog/849589/201710/849589-20171015232453668-1397662527.gif)
## 代码实现
```java
class Solution{
    /**
         * 基数排序
         * @param array
         * @return
         */
        public static int[] RadixSort(int[] array) {
            if (array == null || array.length < 2)
                return array;
            // 1.先算出最大数的位数；
            int max = array[0];
            for (int i = 1; i < array.length; i++) {
                max = Math.max(max, array[i]);
            }
            int maxDigit = 0;
            while (max != 0) {
                max /= 10;
                maxDigit++;
            }
            int mod = 10, div = 1;
            ArrayList<ArrayList<Integer>> bucketList = new ArrayList<ArrayList<Integer>>();
            for (int i = 0; i < 10; i++)
                bucketList.add(new ArrayList<Integer>());
            for (int i = 0; i < maxDigit; i++, mod *= 10, div *= 10) {
                for (int j = 0; j < array.length; j++) {
                    int num = (array[j] % mod) / div;
                    bucketList.get(num).add(array[j]);
                }
                int index = 0;
                for (int j = 0; j < bucketList.size(); j++) {
                    for (int k = 0; k < bucketList.get(j).size(); k++)
                        array[index++] = bucketList.get(j).get(k);
                    bucketList.get(j).clear();
                }
            }
            return array;
        }
}
```