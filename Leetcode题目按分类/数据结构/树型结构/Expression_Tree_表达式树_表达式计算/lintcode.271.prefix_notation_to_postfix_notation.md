
```
解题思路
本题考查的是前缀表达式后缀表达式和表达式树的关系。

表达式树所有的叶节点都是变量，非叶节点就是符号，连接两个子节点，它将获取子节点并且进行运算。表达式树的中序遍历就是我们常见的中缀表达式。前缀表达式就是表达式树的前序遍历，后缀表达式就是表达式树的后序遍历。

本题需要将前缀表达式转换成表达式树的形式，并且进行后序遍历就可以得到后缀表达式。

代码思路
前缀表达式转换成表达式树：

维护一个树节点的栈，倒序遍历前缀表达式：

如果是变量，直接将这个变量的节点压入栈中。
如果是符号，取出栈顶的两个节点，分别作为左右子节点，并将这个节点压入栈中。
最后栈中剩下的节点就是表达式树的根节点。

复杂度分析
设前缀表达式的长度为 NN。

时间复杂度
分割构造表达式树的时间复杂度为 O(N)。
遍历表达式树的时间复杂度为 O(N)。
总时间复杂度为 O(N)。
空间复杂度
构造表达式树要维护一个栈，空间复杂度为 O(N)。
递归遍历表达式树的空间复杂度为 O(N)。
总空间复杂度为 O(N)。
```


```java
public class Solution {
    /**
     * @param str: the prefix notation.
     * @return: return the postfix notation.
     */
    public String prefixNotationToPostfixNotation(String str) {
        String[] tokens = str.split(" ");
        // 建立表达式树
        TreeNode expressionTree = buildTree(tokens);

        //获得表达式树的后序遍历
        List<String> postfix = new ArrayList<>();
        postOrderTraverse(expressionTree, postfix);

        // convert list to string
        StringBuilder result = new StringBuilder();
        for (int i = 0; i < postfix.size(); i++) {
            if (i > 0) result.append(" ");
            result.append(postfix.get(i));
        }
        return result.toString();
    }
    private boolean isOperator(String s) {
        return "+-*/".indexOf(s) != -1;
    }
    private void postOrderTraverse(TreeNode root, List<String> postfix) {
        if (root == null) return;
        postOrderTraverse(root.left, postfix);
        postOrderTraverse(root.right, postfix);
        postfix.add(root.val);
    }
    public TreeNode buildTree(String[] tokens) {
        Deque<TreeNode> nodeStack = new ArrayDeque<>();
        // 倒序遍历前缀表达式构造表达式树
        for (int i = tokens.length - 1; i >= 0; i--) {
            TreeNode node = new TreeNode(tokens[i]);
            if (isOperator(tokens[i])) {  //如果是操作符,取出栈中的两个元素，作为当前元素的子节点
                node.left = nodeStack.pop();
                node.right = nodeStack.pop();
                // 并将这个节点压入栈中
                nodeStack.push(node);

            } else {   //如果是操作数,直接压栈
                nodeStack.offerFirst(node);
            }
        }
        return nodeStack.peek();
    }

    class TreeNode {
         String val;
         TreeNode left, right;
         TreeNode(String s) {
            this.val = s;
            this.left = this.right = null;
        }
    }
}


```