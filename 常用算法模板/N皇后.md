


```java
class Solution {
    boolean[] usedCols;
    boolean[] usedDiag; // 2*n-1條對角線
    boolean[] usedRevDiag;
    
    public List<List<String>> solveNQueens(int n) {
        usedCols = new boolean[n];
        usedDiag = new boolean[2*n-1]; // 2*n-1條對角線
        usedRevDiag = new boolean[2*n-1];  // 2*n-1條反對角線
        
        // n行n列，recursion tree有n層，每層決定一行，每行有N种可能
        
        List<List<String>> ret = new ArrayList<>();
        
        int[] board = new int[n]; // 表示一種方法
        dfs(ret, board, 0, n);  //從第0行開始
        return ret;
    }
    private void dfs(List<List<String>> ret, int[] board, int row, int n) {
        // base case
        if (row == n) {  //最後一行都已經放完了
           ret.add(toList(board));
            return;
        }
        // 依次try當前行的所有列
        for (int col = 0; col < n; col++) {
            // 確定當前列是否可以放棋子
            // check一個十字和兩個對角線的方向
            if (usedCols[col] || usedDiag[col +row] || usedRevDiag[col - row + n - 1]) continue;
            
            usedCols[col] = true;
            usedDiag[col + row] = true;
            usedRevDiag[col - row + n - 1] = true;
            
            board[row] = col;  //把棋子放在第col列
            dfs(ret, board, row + 1, n);
            // backtrack
            usedCols[col] = false;
            usedDiag[col + row] = false;
            usedRevDiag[col - row + n - 1] = false;
            
        }
    }
    private List<String> toList(int[] board) {
        List<String> ret = new ArrayList<>();
        int n = board.length;
        
        for (int i = 0; i < n; i++) {
            String curRow = "";
            for (int j = 0; j < n; j++) {
                if (j == board[i]) { curRow += "Q";
                 } else {
                   curRow += ".";
                }
            }
            ret.add(curRow);
        }
        return ret;
    }
}
```
