# 约瑟夫环
```java
class Solution{
    
    private static int helper(int n, int k) {
        int ans = 0;
        if(n == 0 || k == 0)
            return -1;

        int index = -1;

        LinkedList<Integer> list = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            list.add(i);
        }

        while(list.size() > 1){
            // index现在保存的是删除的下标
            index = (index + k)%list.size();
            list.remove(index);
            index--;
        }

        return list.get(0);
    }
}
```
#### 公式法
f(N,M)=(f(N−1,M)+M)%N
- f(N,M)表示，N个人报数，每报到M时杀掉那个人，最终胜利者的编号
- f(N−1,M)f(N-1,M)f(N−1,M)表示，N-1个人报数，每报到M时杀掉那个人，最终胜利者的编号

每个人被杀的人之间就隔 M 个人，
[参考：约瑟夫环——公式法（递推公式）](https://blog.csdn.net/u011500062/article/details/72855826)