- 后缀表达式是唯一的

- shuangting yard

- 为了把较难处理的中缀表达式转化为容易处理的后缀表达式，Dijsktra（没错又是他）引入了一个算法，称为调度场算法。


```
其实中缀表达式之所以难处理，本质上就是因为运算符优先级。如果我们把表达式全部按优先级加上括号，把3+5*4^2-1变成(3+(5*(4^2)))-1，很容易递归地计算。但是，“按优先级加括号”并不是很好实现，所以我们换一种思路，仍使用栈来解决问题。

为什么要使用栈？因为当我们读到a+b时，我们并不知道后面是a+b+c还是a+b*c，所以不能立刻转化为a b +，而要暂缓一步，把加号存到栈里，等到有了足够的信息时，再把它放出来。

什么时候有足够的信息呢，不妨倒过来想。

假设乘方是表达式里优先级最高的运算，那我们一读到a^b，不管b后面是什么运算符，都可以立刻把它变成a b ^。

对于乘法，可能有a*b^c^d^...这样的长链，但一旦我们读到另一个*或者一个+，形成了a*b^c^d^...*或a*b^c^d^...+的形式，前面那个*便可以放心地放于此符号之前。

而加法也是类似，但是放出来的条件仅有读到另一个+。

归纳一下发现，栈顶的运算符可以被弹出的条件是其优先级不低于新读入的运算符。当然，当表达式结束时，也要把栈里剩余的运算符依次弹出。

一般地，我们采取这样的算法：

依次按顺序读入，
读到数字：直接输出；
读到运算符：如果栈顶的运算符优先级不低于该运算符，则输出栈顶运算符并使之出栈，直到栈空或不满足上述条件为止；然后入栈。
当读入完毕时，依次输出并弹出栈顶运算符，直到栈被清空。
```



https://zhuanlan.zhihu.com/p/93647900

```
处理括号
括号的优先级是多少？你可能条件反射地说，括号的优先级当然是最高的。但是，在调度场算法中，这样的处理会出问题——如果简单地把括号当成一种拥有最高优先级的运算符，当你读到左括号就立刻输出了，这当然不对。

恰恰相反，我们得把左括号的优先级当作最低。不仅如此，还必须在算法中对括号特别处理。注意，后缀表达式里没有括号，所以括号不应该被输出。由于括号里一定是一个完整的表达式，所以可以这样修改算法：

依次按顺序读入，
读到数字：直接输出；
读到一般运算符：如果栈顶的运算符优先级不低于该运算符，则输出栈顶运算符并使之出栈，直到栈空或不满足上述条件为止；然后入栈；
读到左括号：直接入栈；
读到右括号：输出栈顶运算符并使之出栈，直到栈顶为左括号为止；令左括号出栈。
当读入完毕时，依次输出并弹出栈顶运算符，直到栈被清空。


右结合运算符
刚刚我们谈的都是左结合运算符，但有时我们也会处理右结合的        运算符，例如乘方在某些编程语言（如Haskell）中就是右结合的。a^b^c会被理解为a^(b^c)而不是(a^b)^c。C语言中的赋值运算符（=）是一个优先级较低（比上面提到的所有运算符都低）的右结合运算符，我们用它来举例。

考虑这个表达式：x = y = 2 + 3 ，按照上面的算法，我们要先计算后面的y = 2 + 3，所以在遇到第二个=时不能弹出栈顶运算符（不然就成了x y =），而要等到读入+时再弹出。这样我们就看出左结合与右结合的区别了：仅仅在于读入优先级相同的运算符时是否弹出。

存在右结合运算符的算法如下：

依次按顺序读入，
读到数字：直接输出；
读到左结合运算符：如果栈顶的运算符优先级不低于该运算符，则输出栈顶运算符并使之出栈，直到栈空或不满足上述条件为止；然后入栈；
读到右结合运算符：如果栈顶的运算符优先级高于该运算符，则输出栈顶运算符并使之出栈，直到栈空或不满足上述条件为止；然后入栈；
读到左括号：直接入栈；
读到右括号：输出栈顶运算符并使之出栈，直到栈顶为左括号为止；令左括号出栈。
当读入完毕时，依次输出并弹出栈顶运算符，直到栈被清空。
```

![20211111002024](https://raw.githubusercontent.com/corykingsf/hack-interview-handbook/main/image/20211111002024.png)

```
 ‌
1.if‌ ‌operand,‌ ‌append‌ ‌it‌ ‌to‌ ‌the‌ ‌result‌ ‌
2.if‌ ‌operator‌ ‌
a.if‌ ‌“(“,‌ ‌push‌ ‌to‌ ‌stack‌ ‌
b.if‌ ‌“)”,‌ ‌process‌ ‌all‌ ‌the‌ ‌operators‌ ‌(append‌ ‌to‌ ‌the‌ ‌result)‌ ‌in‌ ‌the‌ ‌stack‌ ‌until‌ ‌“(”,‌ ‌finally‌ ‌pop‌ ‌up‌ ‌“(”‌ ‌
c.if‌ ‌“+-*/^”,‌ ‌remove‌ ‌operators‌ ‌which‌ ‌have‌ ‌‌higher‌ ‌or‌ ‌equal‌‌ ‌precedence‌ ‌from‌ ‌the‌ ‌stack‌ ‌and‌ ‌append‌ ‌
them‌ ‌to‌ ‌the‌ ‌result.‌ ‌After‌ ‌that,‌ ‌push‌ ‌this‌ ‌operator‌ ‌to‌ ‌the‌ ‌stack‌ ‌
3.when‌ ‌reach‌ ‌the‌ ‌end‌ ‌of‌ ‌expression,‌ ‌pop‌ ‌the‌ ‌rest‌ ‌operators‌ ‌in‌ ‌the‌ ‌stack‌ ‌to‌ ‌the‌ ‌result.‌ ‌
 ‌
 ```

```java

public‌ ‌String‌ ‌infixToPostfix(String‌ ‌infix)‌ ‌{‌ ‌
    //‌ ‌sanity‌ ‌check‌ ‌
    Map<Character,‌ ‌Integer>‌ ‌prec‌ ‌=‌ ‌new‌ ‌HashMap<>();‌ ‌
    //‌ ‌^‌ ‌->‌ ‌4‌ ‌
    prec.put(‘*’,‌ ‌3);‌ ‌
    prec.put(‘/’,‌ ‌3);‌ ‌
    prec.put(‘+’,‌ ‌2);‌ ‌
    prec.put(‘-’,‌ ‌2);‌ ‌
    prec.put(‘(’,‌ ‌1);‌ ‌
    prec.put(‘)’,‌ ‌1);‌ ‌
    ‌
    Deque<Character>‌ ‌stk‌ ‌=‌ ‌new‌ ‌ArrayDeque<>();‌ ‌
    StringBuilder‌ ‌postfix‌ ‌=‌ ‌new‌ ‌StringBuilder();‌ ‌
    ‌
    //‌ ‌scan‌ ‌from‌ ‌left‌ ‌to‌ ‌right‌ ‌
    for‌ ‌(int‌ ‌i‌ ‌=‌ ‌0;‌ ‌i‌ ‌<‌ ‌infix.length();‌ ‌i++)‌ ‌{‌ ‌
        char‌ ‌ch‌ ‌=‌ ‌infix.charAt(i);‌ ‌
        if‌ ‌(ch‌ ‌==‌ ‌‘(‘)‌ ‌{‌ ‌
             stk.push(ch);‌ ‌
        }‌ ‌else‌ ‌if‌ ‌(ch‌ ‌==‌ ‌‘)’)‌ ‌{‌ ‌
             while‌ ‌(!stk.isEmpty()‌ ‌&&‌ ‌stk.peek()‌ ‌!=‌ ‌‘(‘)‌ ‌{‌ ‌
                 postfix.append(stk.pop());‌ ‌
             }‌ ‌
             //‌ ‌don’t‌ ‌forget‌ ‌to‌ ‌pop‌ ‌up‌ ‌‘(
            stk.pop();‌ ‌
        }‌ ‌else‌ ‌if‌ ‌(isOperator(ch))‌ ‌{‌ ‌
            ‌while‌‌ ‌(!stk.isEmpty()‌ ‌&&‌ ‌prec.get(stk.peek())‌ ‌>=‌ ‌prec.get(ch))‌ ‌{‌ ‌
                postfix.append(stk.pop());‌ ‌
            }‌ ‌
            //‌ ‌don’t‌ ‌forget‌ ‌to‌ ‌push‌ ‌current‌ ‌operator‌ ‌
            stk.push(ch);‌ ‌
        }‌ ‌else‌ ‌{‌ ‌//‌ ‌if‌ ‌operand‌ ‌
           postfix.append(ch);‌ ‌
        }‌ ‌
    }‌ ‌
    ‌
    while‌ ‌(!stk.isEmpty())‌ ‌{‌ ‌
         postfix.append(stk.pop());‌ ‌
    }‌ ‌
    return‌ ‌postfix.toString();‌ ‌
}‌ ‌

```