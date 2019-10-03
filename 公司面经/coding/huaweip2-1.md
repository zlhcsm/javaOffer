图片来自于CSDN用户：[Y -ANG](https://blog.csdn.net/qq_33951180/article/details/52506792)
### 知识回顾
后缀表达式也成为`逆波兰表达式`
#### 中缀表达式转后缀表达式
1，从左到右开始扫描中缀表达式  
2，遇到数字时，加入后缀表达式  
3，遇到运算符时
  - 若为'`(`'，入栈
  - 若为'`)`'，则依次将`栈中的运算符`加入到后缀表达式中，直到出现'`(`'为止，从栈中删除'`(`'
  - 若为除括号外的其它运算符，当优先级高于除'('以外的栈顶运算符时，直接入栈；  
  否则从栈顶开始，
  依次弹出比当前处理的运算符优先级高和优先级相等的运算符，直到出现一个比它优先级低或者遇到了一个左括号为止。
#### 例子说明
中缀表达式：
> A + B x ( C - D ) - E / F

后缀表达式：
> A B C D - x + E F / -

*过程说明*  

字符 | 类型 | 操作 | 后缀表达式结果 | 符号栈
---|---|---|----|---
A | 数字 | 直接加入后缀 | A | 空
+ | 运算符 | 栈为空，加入栈 | A | +
B | 数字 | 直接加入后缀 | A B | +
x | 运算符 | 优先级高于栈顶，加入栈 | A B | + x
( | 左括号 | 入栈 | A B| + x (
C | 数字 | 直接加入后缀 | A B C| + x (
- | 运算符 | 栈顶为（，加入栈 | A B C| + x ( -
D | 数字 | 直接加入后缀 | A B C D| + x ( -
) | 右括号 | 出栈到（以前，出栈加入到后缀 | A B C D -| + x 
- | 运算符 | 优先级最低，清空栈 | A B C D - x +| -
E | 数字 | 直接加入后缀 | A B C D  - x + E| -
/ | 运算符 | 优先级高，入栈 | A B C D - x + E | -/
F | 数字 | 直接加入后缀 | A B C D  - x + E F| - /
空| 空 | 将栈中数据取出| A B C D  - x + E F / - | 空

#### 如何计算后缀表达式
过程说明：  
1，如果是数字则压入栈；  
2，如果是运算符，则取出栈顶的两个元素，进行计算。将结果压入栈顶。  
3，循环这个过程直到遍历结束
![](https://img-blog.csdn.net/20160911225209660?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
#### 代码实现
```java
class Solution{
    //postfix 表示后缀表达式字符串
        public static int postfixValue(String postfix){
            LinkedList<Integer> postList = new LinkedList<>();//模拟栈的功能
            Character nextChar;
            int leftOperator, rightOperator;
            
            for(int i = 0; i < postfix.length(); i++)
            {
                nextChar = postfix.charAt(i);
                if(nextChar >= '0' && nextChar <= '9')//如果是操作数
                    postList.push(nextChar - '0');
                else//如果是运算符
                {
                    rightOperator = postList.pop();
                    leftOperator = postList.pop();
                    switch (nextChar) {
                    case '+':
                        postList.push(leftOperator + rightOperator);
                        break;
                    case '-':
                        postList.push(leftOperator - rightOperator);
                        break;
                    case '*':
                        postList.push(leftOperator * rightOperator);
                        break;
                    case '/':
                        postList.push(leftOperator / rightOperator);
                        break;
                    default:
                        break;
                    }
                }
            }
            return postList.pop();
        }
}

```

