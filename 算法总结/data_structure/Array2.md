### 第一次只出现一次的字符
[剑指offer 面试题50](https://www.nowcoder.com/practice/1c82e8cf713b4bbeb2a5b31cf5b0417c?tpId=13&tqId=11187&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking)

**题目描述**  
>在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.

#### 使用库函数
```java
public class Solution {
    public int FirstNotRepeatingChar(String str) {
        if(str.length() == 0)
            return -1;
        
        for (char c:str.toCharArray()) {
            // 如果一个字符第一次出现的下标==最后一次出现的下标，则表明该字符只出现一次
            if(str.indexOf(c) == str.lastIndexOf(c))
                return str.indexOf(c);
        } 
        
        return 0;
    }
}
```
#### 使用HashMap
```java
import java.util.LinkedHashMap;
public class Solution {
    public int FirstNotRepeatingChar(String str) {
       LinkedHashMap<Character, Integer> map = new LinkedHashMap<Character, Integer>();
       for(int i=0; i < str.length(); i++){
           if(map.containsKey(str.charAt(i))){
               int time = map.get(str.charAt(i));
               map.put(str.charAt(i), ++time);
           }else{
           map.put(str.charAt(i), 1);
           }
       }
       
       int pos = -1;
       int i = 0;
       for(; i<str.length(); i++){
           char c = str.charAt(i);
           if(map.get(c) == 1){
               return i;
           }
       }
       return pos;
    }
}
```
