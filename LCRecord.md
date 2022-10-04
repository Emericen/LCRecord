

# Day#1 (06/06/2022)

Result: X X X X

Time left: 28 minutes

## LC#366. Find Leaves of Binary Tree

Given the `root` of a binary tree, collect a tree's nodes as if you were doing this:

- Collect all the leaf nodes.
- Remove all the leaf nodes.
- Repeat until the tree is empty.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/03/16/remleaves-tree.jpg)

```
Input: root = [1,2,3,4,5]
Output: [[4,5,3],[2],[1]]
Explanation:
[[3,5,4],[2],[1]] and [[3,4,5],[2],[1]] are also considered correct answers since per each level it does not matter the order on which elements are returned.
```

**Example 2:**

```
Input: root = [1]
Output: [[1]]
```

**Constraints:**

- The number of nodes in the tree is in the range `[1, 100]`.
- `-100 <= Node.val <= 100`

**Solution:**

```java
class Solution {
    // TC = O(N)
    // SC = O(N)
    public List<List<Integer>> findLeaves(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        helper(root, result);
        return result;
    }
    
    // R = [[4,5],[2,]]
    //
    //          1
    //      
    //     2         3
    //    0 0   
    //  4     5
    // # #   # #
    private int helper(TreeNode root, List<List<Integer>> result) {
        if (root == null) {
            return -1;
        }
        
        int left = helper(root.left, result);
        int right = helper(root.right, result);
        int height = Math.max(left, right) + 1;
        
        if (result.size() == height) {
            result.add(new ArrayList<>());
        }
        result.get(height).add(root.val);
        
        return height;
    }
}
```



## LC#2096. Step-By-Step Directions From a Binary Tree Node to Another

You are given the `root` of a **binary tree** with `n` nodes. Each node is uniquely assigned a value from `1` to `n`. You are also given an integer `startValue` representing the value of the start node `s`, and a different integer `destValue` representing the value of the destination node `t`.

Find the **shortest path** starting from node `s` and ending at node `t`. Generate step-by-step directions of such path as a string consisting of only the **uppercase** letters `'L'`, `'R'`, and `'U'`. Each letter indicates a specific direction:

- `'L'` means to go from a node to its **left child** node.
- `'R'` means to go from a node to its **right child** node.
- `'U'` means to go from a node to its **parent** node.

Return *the step-by-step directions of the **shortest path** from node* `s` *to node* `t`.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/11/15/eg1.png)

```
Input: root = [5,1,2,3,null,6,4], startValue = 3, destValue = 6
Output: "UURL"
Explanation: The shortest path is: 3 → 1 → 5 → 2 → 6.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/11/15/eg2.png)

```
Input: root = [2,1], startValue = 2, destValue = 1
Output: "L"
Explanation: The shortest path is: 2 → 1.
```

 

**Constraints:**

- The number of nodes in the tree is `n`.
- `2 <= n <= 105`
- `1 <= Node.val <= n`
- All the values in the tree are **unique**.
- `1 <= startValue, destValue <= n`
- `startValue != destValue`

**Solution:**

```java
class Solution {
    private boolean find(TreeNode n, int val, StringBuilder sb) {
        if (n.val == val) 
            return true;
        if (n.left != null && find(n.left, val, sb))
            sb.append("L");
        else if (n.right != null && find(n.right, val, sb))
            sb.append("R");
        return sb.length() > 0;
    }
    
    public String getDirections(TreeNode root, int startValue, int destValue) {
        StringBuilder s = new StringBuilder(), d = new StringBuilder();
        find(root, startValue, s);
        find(root, destValue, d);
        int i = 0, max_i = Math.min(d.length(), s.length());
        while (i < max_i && s.charAt(s.length() - i - 1) == d.charAt(d.length() - i - 1))
            ++i;
        return "U".repeat(s.length() - i) + d.reverse().toString().substring(i);
    }
}
```



## LC#818. Race Car

Your car starts at position `0` and speed `+1` on an infinite number line. Your car can go into negative positions. Your car drives automatically according to a sequence of instructions `'A'` (accelerate) and `'R'` (reverse):

- When you get an instruction

  ```
  'A'
  ```

  , your car does the following:

  - `position += speed`
  - `speed *= 2`

- When you get an instruction

  ```
  'R'
  ```

  , your car does the following:

  - If your speed is positive then `speed = -1`
  - otherwise `speed = 1`

  Your position stays the same.

For example, after commands `"AAR"`, your car goes to positions `0 --> 1 --> 3 --> 3`, and your speed goes to `1 --> 2 --> 4 --> -1`.

Given a target position `target`, return *the length of the shortest sequence of instructions to get there*.

**Example 1:**

```
Input: target = 3
Output: 2
Explanation: 
The shortest instruction sequence is "AA".
Your position goes from 0 --> 1 --> 3.
```

**Example 2:**

```
Input: target = 6
Output: 5
Explanation: 
The shortest instruction sequence is "AAARA".
Your position goes from 0 --> 1 --> 3 --> 7 --> 7 --> 6.
```

**Constraints:**

- `1 <= target <= 104`

**BFS Solution**

Notice how we switched from DFS to BFS in situations like this

```java
class Solution {
    
    //                       (0,1)
    //                 /                 \
    //             (1,2)                  (0,-1)
    //         /           \            /        \
    //      (3,4)         (1,-1)    (-1,-2)     (0,1)
    //    /       \       /
    // (7,8)    (3,3)  (0,-2)
    //          /    \
    //       (6,6)  (3,2)
 
    public int racecar(int target) {
        Node start = new Node(0, 1);
        
        Queue<Node> queue = new LinkedList<>();
        queue.offer(start);
        
        Set<String> visited = new HashSet<>();
        visited.add(start.toString());
        
        for (int level = 0; !queue.isEmpty(); level++) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                Node cur = queue.poll();
                
                if (cur.position == target) {
                    return level;
                }
                
                Node next1 = new Node(cur.position + cur.speed, cur.speed * 2);
                if (!visited.contains(next1.toString()) && 
                    0 < next1.position && next1.position < (target * 2)) {
                    queue.offer(next1);
                    visited.add(next1.toString());
                }
                
                Node next2 = new Node(cur.position, cur.speed > 0 ? -1 : 1);
                if (!visited.contains(next2.toString()) && 
                    0 < next2.position && next2.position < (target * 2)) {
                    queue.offer(next2);
                    visited.add(next2.toString());
                }
            }
        }
        return -1;
    }
}

class Node {
    public int position;
    public int speed;
    public Node(int position, int speed) {
        this.position = position;
        this.speed = speed;
    }
    
    @Override
    public String toString() {
        return "(" + position + ", " + speed + ")";
    } 
}
```



**Bottom-up DP Solution**

```java
public int racecar(int target) {
    int[] dp = new int[target + 1];
    
    for (int i = 1; i <= target; i++) {
        dp[i] = Integer.MAX_VALUE;
        
        int m = 1, j = 1;
        
        for (; j < i; j = (1 << ++m) - 1) {
            for (int q = 0, p = 0; p < j; p = (1 << ++q) - 1) {
                dp[i] = Math.min(dp[i], m + 1 + q + 1 + dp[i - (j - p)]);
            }
        }
        
        dp[i] = Math.min(dp[i], m + (i == j ? 0 : 1 + dp[j - i]));
    }   
    return dp[target];
}
```





## LC#1293. Shortest Path in a Grid with Obstacles Elimination

You are given an `m x n` integer matrix `grid` where each cell is either `0` (empty) or `1` (obstacle). You can move up, down, left, or right from and to an empty cell in **one step**.

Return *the minimum number of **steps** to walk from the upper left corner* `(0, 0)` *to the lower right corner* `(m - 1, n - 1)` *given that you can eliminate **at most*** `k` *obstacles*. If it is not possible to find such walk return `-1`.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/09/30/short1-grid.jpg)

```
Input: grid = [[0,0,0],[1,1,0],[0,0,0],[0,1,1],[0,0,0]], k = 1
Output: 6
Explanation: 
The shortest path without eliminating any obstacle is 10.
The shortest path with one obstacle elimination at position (3,2) is 6. Such path is (0,0) -> (0,1) -> (0,2) -> (1,2) -> (2,2) -> (3,2) -> (4,2).
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/09/30/short2-grid.jpg)

```
Input: grid = [[0,1,1],[1,1,1],[1,0,0]], k = 1
Output: -1
Explanation: We need to eliminate at least two obstacles to find such a walk.
```

 

**Constraints:**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 40`
- `1 <= k <= m * n`
- `grid[i][j]` is either `0` **or** `1`.
- `grid[0][0] == grid[m - 1][n - 1] == 0`

**Solution**

Yet another example of BFS, notice how we can also do hashing of wrapper class differently here

```java
class Solution {
    public int shortestPath(int[][] grid, int k) {
        final int N = grid.length;
        final int M = grid[0].length;

        Queue<StepState> queue = new LinkedList<>();
        Set<StepState> visited = new HashSet<>();
        
        StepState start = new StepState(0, 0, 0, k);
        queue.offer(start);
        visited.add(start);
        
        while (!queue.isEmpty()) {
            StepState cur = queue.poll();
            
            if (cur.row == N - 1 && cur.col == M - 1) {
                // Reached
                return cur.steps;
            }
            
            int[] nextSteps = 
            {cur.row, cur.col + 1, cur.row + 1, cur.col, cur.row ,cur.col - 1 ,cur.row - 1, cur.col};
            
            for (int i = 0; i < nextSteps.length; i += 2) {
                int nextRow = nextSteps[i];
                int nextCol = nextSteps[i + 1];
                
                if (0 > nextRow || nextRow == N || 0 > nextCol || nextCol == M) {
                    // OOB
                    continue;
                }
                
                int nextElim = cur.k - grid[nextRow][nextCol];
                StepState newState = new StepState(cur.steps + 1, nextRow, nextCol, nextElim);
                if (nextElim >= 0 && !visited.contains(newState)) {
                    visited.add(newState);
                    queue.offer(newState);
                }
            }
        }
        return -1;
    }
}

class StepState {
    public int steps;
    public int row;
    public int col; 
    public int k;
    
    public StepState(int steps, int row, int col, int k) {
        this.steps = steps;
        this.row = row;
        this.col = col;
        this.k = k;
    }
    
    @Override
    public int hashCode() {
        return (this.row + 1) * (this.col + 1) * this.k;
    }
    
    @Override
    public boolean equals(Object other) {
        if (!(other instanceof StepState)) {
            return false;
        }
        StepState newState = (StepState) other;
        return 
            (this.row == newState.row) && 
            (this.col == newState.col) && 
            (this.k == newState.k);
    }
    
    @Override
    public String toString() {
        return String.format("%d %d %d", this.row, this.col, this.k);
    }
}
```





# Day#2 (06/07/2022)

Result:

2128 X, 150 O, 2158 X, 127 O



## LC#2128. Remove All Ones With Row and Column Flips

You are given an `m x n` binary matrix `grid`.

In one operation, you can choose **any** row or column and flip each value in that row or column (i.e., changing all `0`'s to `1`'s, and all `1`'s to `0`'s).

Return `true` *if it is possible to remove all* `1`*'s from* `grid` using **any** number of operations or `false` otherwise.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2022/01/03/image-20220103191300-1.png)

```
Input: grid = [[0,1,0],[1,0,1],[0,1,0]]
Output: true
Explanation: One possible way to remove all 1's from grid is to:
- Flip the middle row
- Flip the middle column
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2022/01/03/image-20220103181204-7.png)

```
Input: grid = [[1,1,0],[0,0,0],[0,0,0]]
Output: false
Explanation: It is impossible to remove all 1's from grid.
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2022/01/03/image-20220103181224-8.png)

```
Input: grid = [[0]]
Output: true
Explanation: There are no 1's in grid.
```

 

**Constraints:**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 300`
- `grid[i][j]` is either `0` or `1`.

**Solution**

```java
public class Solution {
   public boolean removeOnes(int[][] g) {
      int m = g.length, n = g[0].length;
      
      IntStream.range(0,n).forEach(j -> {
         if (g[0][j] == 1) flipColumn(j,g); 
      });
      
      for (int i = 1; i < m; i++) {
         for (int j = 1; j < n; j++) {
             if (g[i][j] != g[i][j-1]) return false;
         }
      }
      return true;
   }

   private void flipColumn(int j, int[][] g) {
      IntStream.range(0,g.length).forEach(i -> {
         g[i][j] = 1 - g[i][j];
      });
   }
}
```



## LC#2158. Amount of New Area Painted Each Day

There is a long and thin painting that can be represented by a number line. You are given a **0-indexed** 2D integer array `paint` of length `n`, where `paint[i] = [starti, endi]`. This means that on the `ith` day you need to paint the area **between** `starti` and `endi`.

Painting the same area multiple times will create an uneven painting so you only want to paint each area of the painting at most **once**.

Return *an integer array* `worklog` *of length* `n`*, where* `worklog[i]` *is the amount of **new** area that you painted on the* `ith` *day.*

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2022/02/01/screenshot-2022-02-01-at-17-16-16-diagram-drawio-diagrams-net.png)

```
Input: paint = [[1,4],[4,7],[5,8]]
Output: [3,3,1]
Explanation:
On day 0, paint everything between 1 and 4.
The amount of new area painted on day 0 is 4 - 1 = 3.
On day 1, paint everything between 4 and 7.
The amount of new area painted on day 1 is 7 - 4 = 3.
On day 2, paint everything between 7 and 8.
Everything between 5 and 7 was already painted on day 1.
The amount of new area painted on day 2 is 8 - 7 = 1. 
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2022/02/01/screenshot-2022-02-01-at-17-17-45-diagram-drawio-diagrams-net.png)

```
Input: paint = [[1,4],[5,8],[4,7]]
Output: [3,3,1]
Explanation:
On day 0, paint everything between 1 and 4.
The amount of new area painted on day 0 is 4 - 1 = 3.
On day 1, paint everything between 5 and 8.
The amount of new area painted on day 1 is 8 - 5 = 3.
On day 2, paint everything between 4 and 5.
Everything between 5 and 7 was already painted on day 1.
The amount of new area painted on day 2 is 5 - 4 = 1. 
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2022/02/01/screenshot-2022-02-01-at-17-19-49-diagram-drawio-diagrams-net.png)

```
Input: paint = [[1,5],[2,4]]
Output: [4,0]
Explanation:
On day 0, paint everything between 1 and 5.
The amount of new area painted on day 0 is 5 - 1 = 4.
On day 1, paint nothing because everything between 2 and 4 was already painted on day 0.
The amount of new area painted on day 1 is 0.
```

 

**Constraints:**

- `1 <= paint.length <= 105`
- `paint[i].length == 2`
- `0 <= starti < endi <= 5 * 104`

**Solution**

```java
class Solution {
    // OMFG it passed
    public int[] amountPainted(int[][] paint) {
        boolean[] painted = new boolean[50001];
        int[] ans = new int[paint.length];
        for (int i = 0; i < ans.length; i++) {
            int start = paint[i][0];
            int end = paint[i][1];
            int amount = 0;
            for (int j = start; j < end; j++) {
                if (!painted[j]) {
                    painted[j] = true;
                    amount += 1;
                }
            }
            ans[i] = amount;
        }
        return ans;
    }
}
```





# Day#3 (06/08/2022)

Result:

2104 O, 472 O, 828 X, 588 X

Time remaining: 40 minutes

## LC#828. Count Unique Characters of All Substrings of a Given String

Let's define a function `countUniqueChars(s)` that returns the number of unique characters on `s`.

- For example, calling `countUniqueChars(s)` if `s = "LEETCODE"` then `"L"`, `"T"`, `"C"`, `"O"`, `"D"` are the unique characters since they appear only once in `s`, therefore `countUniqueChars(s) = 5`.

Given a string `s`, return the sum of `countUniqueChars(t)` where `t` is a substring of `s`.

Notice that some substrings can be repeated so in this case you have to count the repeated ones too.

 

**Example 1:**

```
Input: s = "ABC"
Output: 10
Explanation: All possible substrings are: "A","B","C","AB","BC" and "ABC".
Every substring is composed with only unique letters.
Sum of lengths of all substring is 1 + 1 + 1 + 2 + 2 + 3 = 10
```

**Example 2:**

```
Input: s = "ABA"
Output: 8
Explanation: The same as example 1, except countUniqueChars("ABA") = 1.
```

**Example 3:**

```
Input: s = "LEETCODE"
Output: 92
```

 

**Constraints:**

- `1 <= s.length <= 105`
- `s` consists of uppercase English letters only.

**Solution:**

```java
class Solution {
    public int uniqueLetterString(String S) {
        int[][] index = new int[26][2];
        for (int i = 0; i < 26; ++i) Arrays.fill(index[i], -1);
        int res = 0, N = S.length(), mod = (int)Math.pow(10, 9) + 7;
        for (int i = 0; i < N; ++i) {
            int c = S.charAt(i) - 'A';
            res = (res + (i - index[c][1]) * (index[c][1] - index[c][0]) % mod) % mod;
            index[c] = new int[] {index[c][1], i};
        }
        for (int c = 0; c < 26; ++c)
            res = (res + (N - index[c][1]) * (index[c][1] - index[c][0]) % mod) % mod;
        return res;
    }
}
```



## LC#588. Design In-Memory File System

Design a data structure that simulates an in-memory file system.

Implement the FileSystem class:

- `FileSystem()` Initializes the object of the system.

- ```
  List<String> ls(String path)
  ```

  - If `path` is a file path, returns a list that only contains this file's name.
  - If `path` is a directory path, returns the list of file and directory names **in this directory**.

  The answer should in

   

  lexicographic order

  .

- `void mkdir(String path)` Makes a new directory according to the given `path`. The given directory path does not exist. If the middle directories in the path do not exist, you should create them as well.

- ```
  void addContentToFile(String filePath, String content)
  ```

  - If `filePath` does not exist, creates that file containing given `content`.
  - If `filePath` already exists, appends the given `content` to original content.

- `String readContentFromFile(String filePath)` Returns the content in the file at `filePath`.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/04/28/filesystem.png)

```
Input
["FileSystem", "ls", "mkdir", "addContentToFile", "ls", "readContentFromFile"]
[[], ["/"], ["/a/b/c"], ["/a/b/c/d", "hello"], ["/"], ["/a/b/c/d"]]
Output
[null, [], null, null, ["a"], "hello"]

Explanation
FileSystem fileSystem = new FileSystem();
fileSystem.ls("/");                         // return []
fileSystem.mkdir("/a/b/c");
fileSystem.addContentToFile("/a/b/c/d", "hello");
fileSystem.ls("/");                         // return ["a"]
fileSystem.readContentFromFile("/a/b/c/d"); // return "hello"
```

 

**Constraints:**

- `1 <= path.length, filePath.length <= 100`
- `path` and `filePath` are absolute paths which begin with `'/'` and do not end with `'/'` except that the path is just `"/"`.
- You can assume that all directory names and file names only contain lowercase letters, and the same names will not exist in the same directory.
- You can assume that all operations will be passed valid parameters, and users will not attempt to retrieve file content or list a directory or file that does not exist.
- `1 <= content.length <= 50`
- At most `300` calls will be made to `ls`, `mkdir`, `addContentToFile`, and `readContentFromFile`.



**Solution**

```java
import java.util.*;

public class FileSystem {

	private Directory root;

	public FileSystem() {
		root = new Directory("");
	}

	private Directory resolvePath(String[] path, boolean trim, boolean createIfNotFound) {
		Directory cur = root;
		for (int i = 0; i < path.length - (trim ? 1 : 0); i++) {
			Entity next = cur.search(path[i]);
			if (next != null && next instanceof Directory) {
				cur = (Directory) next;
			} else if (createIfNotFound) {
				Directory newDir = new Directory(path[i]);
				cur.add(newDir);
				cur = newDir;
			} else {
				// Throw exception here.
				return null;
			}
		}
		return cur;
	}

	public List<String> ls(String pathStr) {
		String[] path = pathStr.equals("/") ? new String[0] : pathStr.substring(1).split("/");
		Directory last = resolvePath(path, false, false);
		if (last == null) {
			return new ArrayList<String>();
		}
		return last.list();
	}

	public void mkdir(String pathStr) {
		String[] path = pathStr.equals("/") ? new String[0] : pathStr.substring(1).split("/");
		resolvePath(path, false, true);
	}

	public void addContentToFile(String filePath, String content) {
		String[] path = filePath.substring(1).split("/");
		String fileName = path[path.length - 1];
		Directory parent = resolvePath(path, true, true);

		Entity file = parent.search(fileName);
		if (file == null || !(file instanceof File)) {
			file = new File(fileName);
			parent.add(file);
		}
		((File) file).addContent(content);
	}

	public String readContentFromFile(String filePath) {
		String[] path = filePath.substring(1).split("/");
		String fileName = path[path.length - 1];
		Directory parent = resolvePath(path, true, false);

		Entity file = parent.search(fileName);
		if (file == null || !(file instanceof File)) {
			// Throw exception here
			return "";
		}
		return ((File) file).readContent();
	}


	public static void main(String args[]) {
		FileSystem fs = new FileSystem();
		// fs.mkdir("/Media/Images");
		// fs.mkdir("/Media/Texts");
		// fs.mkdir("/Media/Videos");
		// fs.addContentToFile("/Media/Images/img2.jpg", ":)");
		// fs.addContentToFile("/Media/Images/img1.jpg", ">:(");
		// fs.addContentToFile("/Media/Images/img3.jpg", "XD");
		// fs.addContentToFile("/Media/Texts/text1.txt", "Shakespear");
		// fs.addContentToFile("/Media/Texts/text2.txt", "Jane Austin");
		// fs.addContentToFile("/Media/Videos/ClipHD.mp3", "8===>...");

		// System.out.println("1." + fs.ls("/"));
		// System.out.println("2." + fs.ls("/Media"));
		// System.out.println("3." + fs.ls("/Media/Images"));
		// System.out.println("4." + fs.ls("/Media/Texts"));
		// System.out.println("5." + fs.ls("/Media/Videos"));
	}
}

class Entity {
	private String name;

	public Entity(String name) {
		this.name = name;
	}

	@Override
	public String toString() {
		return this.name;
	}
}

class File extends Entity {

	private String content;

	public File(String name) {
		super(name);
	}

	public void addContent(String content) {
		this.content += content;
	}

	public String readContent() {
		return this.content;
	}
}

class Directory extends Entity {
	private List<Entity> content;

	// TODO: Add set to optimize search

	public Directory(String name) {
		super(name);
		content = new ArrayList<>();
	}

	public void add(Entity en) {
		this.content.add(en);
	}

	public Entity search(String name) {
		for (Entity entity : content) {
			if (entity.toString().equals(name)) {
				return entity;
			}
		}
		return null;
	}

	public List<String> list() {
		List<String> result = new ArrayList<>();
		for (Entity entity : content) {
			result.add(entity.toString());
		}
		result.sort(new LexicalComparator());
		return result;
	}
}

class LexicalComparator implements Comparator<String> {
	@Override
	public int compare(String a, String b) {
		return a.compareTo(b);
	}
}

```





# Day#4 (06/09/2022)

Result: XXXX

Time remaining: 28mins

## LC#400. Nth Digit

Given an integer `n`, return the `nth` digit of the infinite integer sequence `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ...]`.

**Example 1:**

```
Input: n = 3
Output: 3
```

**Example 2:**

```
Input: n = 11
Output: 0
Explanation: The 11th digit of the sequence 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ... is a 0, which is part of the number 10.
```

**Constraints:**

- `1 <= n <= 231 - 1`

**Solution:**

```java
class Solution {
    public int findNthDigit(int n) {
        // 10th = 1
        // 11th = 0
        // 12th = 1
        // 13th = 1
        // 14th = 1
        // 15th = 2
        // 16th = 1
        // 17th = 3
        //
        //     1                   2                   3
        // ... 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 ...
        // ... 1 0 1 1 1 2 1 3 1 4 1 5 1 6 1 7 1 8 1 9 2 0 2 1 ...
        
        int len = 1;
		long count = 9;
		int start = 1;

		while (n > len * count) {
			n -= len * count;
			len += 1;
			count *= 10;
			start *= 10;
		}

		start += (n - 1) / len;
		String s = Integer.toString(start);
		return Character.getNumericValue(s.charAt((n - 1) % len));
        
    }
}
```



## LC#489. Robot Room Cleaner

You are controlling a robot that is located somewhere in a room. The room is modeled as an `m x n` binary grid where `0` represents a wall and `1` represents an empty slot.

The robot starts at an unknown location in the room that is guaranteed to be empty, and you do not have access to the grid, but you can move the robot using the given API `Robot`.

You are tasked to use the robot to clean the entire room (i.e., clean every empty cell in the room). The robot with the four given APIs can move forward, turn left, or turn right. Each turn is `90` degrees.

When the robot tries to move into a wall cell, its bumper sensor detects the obstacle, and it stays on the current cell.

Design an algorithm to clean the entire room using the following APIs:

```
interface Robot {
  // returns true if next cell is open and robot moves into the cell.
  // returns false if next cell is obstacle and robot stays on the current cell.
  boolean move();

  // Robot will stay on the same cell after calling turnLeft/turnRight.
  // Each turn will be 90 degrees.
  void turnLeft();
  void turnRight();

  // Clean the current cell.
  void clean();
}
```

**Note** that the initial direction of the robot will be facing up. You can assume all four edges of the grid are all surrounded by a wall.

 

**Custom testing:**

The input is only given to initialize the room and the robot's position internally. You must solve this problem "blindfolded". In other words, you must control the robot using only the four mentioned APIs without knowing the room layout and the initial robot's position.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/07/17/lc-grid.jpg)

```
Input: room = [[1,1,1,1,1,0,1,1],[1,1,1,1,1,0,1,1],[1,0,1,1,1,1,1,1],[0,0,0,1,0,0,0,0],[1,1,1,1,1,1,1,1]], row = 1, col = 3
Output: Robot cleaned all rooms.
Explanation: All grids in the room are marked by either 0 or 1.
0 means the cell is blocked, while 1 means the cell is accessible.
The robot initially starts at the position of row=1, col=3.
From the top left corner, its position is one row below and three columns right.
```

**Example 2:**

```
Input: room = [[1]], row = 0, col = 0
Output: Robot cleaned all rooms.
```

 

**Constraints:**

- `m == room.length`
- `n == room[i].length`
- `1 <= m <= 100`
- `1 <= n <= 200`
- `room[i][j]` is either `0` or `1`.
- `0 <= row < m`
- `0 <= col < n`
- `room[row][col] == 1`
- All the empty cells can be visited from the starting position.

**Solution:** Spiral Backtracking

**Concepts to use**

Let's use here two programming concepts.

> The first one is called *constrained programming*.

That basically means to put restrictions after each robot move. Robot moves, and the cell is marked as `visited`. That propagates *constraints* and helps to reduce the number of combinations to consider.

![bla](https://leetcode.com/problems/robot-room-cleaner/Figures/489/489_constraints.png)

> The second one called *backtracking*.

Let's imagine that after several moves the robot is surrounded by the visited cells. But several steps before there was a cell which proposed an alternative path to go. That path wasn't used and hence the room is not yet cleaned up. What to do? *To backtrack*. That means to come back to that cell, and to explore the alternative path.

![bla](https://leetcode.com/problems/robot-room-cleaner/Figures/489/489_backtrack.png)

**Intuition**

This solution is based on the same idea as maze solving algorithm called [right-hand rule](https://en.wikipedia.org/wiki/Maze_solving_algorithm#Wall_follower). Go forward, cleaning and marking all the cells on the way as visited. At the obstacle *turn right*, again go forward, *etc*. Always *turn right* at the obstacles and then go forward. Consider already visited cells as virtual obstacles.

> What do do if after the right turn there is an obstacle just in front ?

*Turn right* again.

> How to explore the alternative paths from the cell ?

Go back to that cell and then *turn right* from your last explored direction.

> When to stop ?

Stop when you explored all possible paths, *i.e.* all `4` directions (up, right, down, and left) for each visited cell.

**Algorithm**

Time to write down the algorithm for the backtrack function `backtrack(cell = (0, 0), direction = 0)`.

- Mark the cell as visited and clean it up.
- Explore `4` directions : `up`, `right`, `down`, and `left` (the order is important since the idea is always to turn right) :
  - Check the next cell in the chosen direction :
    - If it's not visited yet and there is no obtacles :
      - Move forward.
      - Explore next cells `backtrack(new_cell, new_direction)`.
      - Backtrack, *i.e.* go back to the previous cell.
    - Turn right because now there is an obstacle (or a virtual obstacle) just in front.

**Implementation**

![bla](https://leetcode.com/problems/robot-room-cleaner/Figures/489/489_implementation.png)

**Code**

```java
class Solution {
  // going clockwise : 0: 'up', 1: 'right', 2: 'down', 3: 'left'
  int[][] directions = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
  Set<Pair<Integer, Integer>> visited = new HashSet();
  Robot robot;

  public void goBack() {
    robot.turnRight();
    robot.turnRight();
    robot.move();
    robot.turnRight();
    robot.turnRight();
  }

  public void backtrack(int row, int col, int d) {
    visited.add(new Pair(row, col));
    robot.clean();
    // going clockwise : 0: 'up', 1: 'right', 2: 'down', 3: 'left'
    for (int i = 0; i < 4; ++i) {
      int newD = (d + i) % 4;
      int newRow = row + directions[newD][0];
      int newCol = col + directions[newD][1];

      if (!visited.contains(new Pair(newRow, newCol)) && robot.move()) {
        backtrack(newRow, newCol, newD);
        goBack();
      }
      // turn the robot following chosen direction : clockwise
      robot.turnRight();
    }
  }

  public void cleanRoom(Robot robot) {
    this.robot = robot;
    backtrack(0, 0, 0);
  }
}
```



## LC#2272. Substring With Largest Variance

The **variance** of a string is defined as the largest difference between the number of occurrences of **any** `2` characters present in the string. Note the two characters may or may not be the same.

Given a string `s` consisting of lowercase English letters only, return *the **largest variance** possible among all **substrings** of* `s`.

A **substring** is a contiguous sequence of characters within a string.

 

**Example 1:**

```
Input: s = "aababbb"
Output: 3
Explanation:
All possible variances along with their respective substrings are listed below:
- Variance 0 for substrings "a", "aa", "ab", "abab", "aababb", "ba", "b", "bb", and "bbb".
- Variance 1 for substrings "aab", "aba", "abb", "aabab", "ababb", "aababbb", and "bab".
- Variance 2 for substrings "aaba", "ababbb", "abbb", and "babb".
- Variance 3 for substring "babbb".
Since the largest possible variance is 3, we return it.
```

**Example 2:**

```
Input: s = "abcde"
Output: 0
Explanation:
No letter occurs more than once in s, so the variance of every substring is 0.
```

 

**Constraints:**

- `1 <= s.length <= 104`
- `s` consists of lowercase English letters.

**Solution:** Kadane's Algorithm

```java
class Solution {
    // TC = O(N)
    // SC = O(1)
    public int largestVariance(String s) {
        int variance = 0;
        
        Set<Character> set = new HashSet<>();
        for (int i = 0; i < s.length(); i++) {
            set.add(s.charAt(i));
        }
        
        for (char a : set) {
            for (char b : set) {
                int var = 0;
                boolean hasB = false;
                boolean firstB = false;
                for (int i = 0; i < s.length(); i++) {
                    var += s.charAt(i) == a ? 1 : 0;
                    if (s.charAt(i) == b) {
                        hasB = true;
                        if (firstB && var >= 0) {
                            firstB = false;
                        } else if (--var < 0) {
                            firstB = true;
                            var = -1;
                        }
                    }
                    variance = Math.max(variance, hasB ? var : 0);
                }
            }
        }
        return variance;
    }
}
```



## LC#1530. Number of Good Leaf Nodes Pairs

You are given the `root` of a binary tree and an integer `distance`. A pair of two different **leaf** nodes of a binary tree is said to be good if the length of **the shortest path** between them is less than or equal to `distance`.

Return *the number of good leaf node pairs* in the tree.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/07/09/e1.jpg)

```
Input: root = [1,2,3,null,4], distance = 3
Output: 1
Explanation: The leaf nodes of the tree are 3 and 4 and the length of the shortest path between them is 3. This is the only good pair.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/07/09/e2.jpg)

```
Input: root = [1,2,3,4,5,6,7], distance = 3
Output: 2
Explanation: The good pairs are [4,5] and [6,7] with shortest path = 2. The pair [4,6] is not good because the length of ther shortest path between them is 4.
```

**Example 3:**

```
Input: root = [7,1,4,6,null,5,3,null,null,null,null,null,2], distance = 3
Output: 1
Explanation: The only good pair is [2,5].
```

 

**Constraints:**

- The number of nodes in the `tree` is in the range `[1, 210].`
- `1 <= Node.val <= 100`
- `1 <= distance <= 10`

**Solution:**

```java
class Solution {
    public int countPairs(TreeNode root, int distance) {
        int[] sum = new int[1];
        helper(root, distance, sum);
        return sum[0];
    }
    
    private List<Integer> helper(TreeNode root, int distance, int[] sum) {
        if (root == null) {
            return new ArrayList<>();
        }
        
        List<Integer> left = helper(root.left, distance, sum);
        List<Integer> right = helper(root.right, distance, sum);
        List<Integer> ret = new ArrayList<>();
        
        if (left.isEmpty() && right.isEmpty()) {
            ret.add(1);
            return ret;
        } 
        
        for (int i = 0; i < left.size(); i++) {
            for (int j = 0; j < right.size(); j++) {
                if (left.get(i) + right.get(j) <= distance) {
                    sum[0]++;
                }
            }
        }
        
        for (int i = 0; i < left.size(); i++) {
            ret.add(left.get(i) + 1);
        }
        
        for (int i = 0; i < right.size(); i++) {
            ret.add(right.get(i) + 1);
        }
        
        return ret;
    }
}
```





# Day#5 (06/10/2022)

Result ~~~~

Time left: 12min

## LC#1268. Search Suggestions System

You are given an array of strings `products` and a string `searchWord`.

Design a system that suggests at most three product names from `products` after each character of `searchWord` is typed. Suggested products should have common prefix with `searchWord`. If there are more than three products with a common prefix return the three lexicographically minimums products.

Return *a list of lists of the suggested products after each character of* `searchWord` *is typed*.

 

**Example 1:**

```
Input: products = ["mobile","mouse","moneypot","monitor","mousepad"], searchWord = "mouse"
Output: [
["mobile","moneypot","monitor"],
["mobile","moneypot","monitor"],
["mouse","mousepad"],
["mouse","mousepad"],
["mouse","mousepad"]
]
Explanation: products sorted lexicographically = ["mobile","moneypot","monitor","mouse","mousepad"]
After typing m and mo all products match and we show user ["mobile","moneypot","monitor"]
After typing mou, mous and mouse the system suggests ["mouse","mousepad"]
```

**Example 2:**

```
Input: products = ["havana"], searchWord = "havana"
Output: [["havana"],["havana"],["havana"],["havana"],["havana"],["havana"]]
```

**Example 3:**

```
Input: products = ["bags","baggage","banner","box","cloths"], searchWord = "bags"
Output: [["baggage","bags","banner"],["baggage","bags","banner"],["baggage","bags"],["bags"]]
```

 

**Constraints:**

- `1 <= products.length <= 1000`
- `1 <= products[i].length <= 3000`
- `1 <= sum(products[i].length) <= 2 * 104`
- All the strings of `products` are **unique**.
- `products[i]` consists of lowercase English letters.
- `1 <= searchWord.length <= 1000`
- `searchWord` consists of lowercase English letters.

**Solution**

```java
class Solution {
    public List<List<String>> suggestedProducts(String[] products, String searchWord) {
        List<List<String>> result = new ArrayList<>();
        Trie trie = new Trie();
        for (String product : products) {
            trie.insert(product);
        }
        for (int i = 1; i <= searchWord.length(); i++) {
            result.add(trie.getBeginning(searchWord.substring(0, i)));
        }
        return result;
    }
}

class Trie {
	private Node root;

	public Trie() {
		root = new Node();
	}

	public boolean search(String word) {
		Node cur = root;
		for (int i = 0; i < word.length(); i++) {
			Node next = cur.children.get(word.charAt(i));
			if (next == null)
				return false;
			cur = next;
		}
		return cur.isWord;
	}

    public List<String> getBeginning(String word) {
        List<String> result = new ArrayList<>();
        Node cur = root;
        for (int i = 0; i < word.length(); i++) {
            Node next = cur.children.get(word.charAt(i));
			if (next == null) return result;
			cur = next;
        }
        
        StringBuilder sb = new StringBuilder(word);
        helper(cur, sb, result);
        return result;
        // result.sort(new MyComparator());
        // return result.size() > 3 ? result.subList(0, 3) : result;
    }
    
    private void helper(Node root, StringBuilder sb, List<String> result) {
        if (root.isWord) {
            result.add(sb.toString());
        }
        
        for (Character next : root.children.keySet()) {
            sb.append(next);
            helper(root.children.get(next), sb, result);
            sb.deleteCharAt(sb.length() - 1);
        }
    }
    
	public boolean insert(String word) {
		if (search(word)) {
			return false;
		}

		Node cur = root;
		for (int i = 0; i < word.length(); i++) {
			Node next = cur.children.get(word.charAt(i));
			if (next == null) {
				next = new Node();
				cur.children.put(word.charAt(i), next);
			}
			cur = next;
		}
		cur.isWord = true;
		return true;
	}
}

class Node {
	public boolean isWord;
	public Map<Character, Node> children;
	public Node() {
		this.children = new HashMap<>();
	}
}

// class MyComparator implements Comparator<String> {
//     @Override
//     public int compare(String a, String b) {
//         return a.compareTo(b);
//     }
// }
```



## LC#273. Integer to English Words

Convert a non-negative integer `num` to its English words representation.

**Example 1:**

```
Input: num = 123
Output: "One Hundred Twenty Three"
```

**Example 2:**

```
Input: num = 12345
Output: "Twelve Thousand Three Hundred Forty Five"
```

**Example 3:**

```
Input: num = 1234567
Output: "One Million Two Hundred Thirty Four Thousand Five Hundred Sixty Seven"
```

 

**Constraints:**

- `0 <= num <= 231 - 1`

**Solution:**

```java
class Solution {
	public String one(int num) {
		switch (num) {
			case 1:
				return "One";
			case 2:
				return "Two";
			case 3:
				return "Three";
			case 4:
				return "Four";
			case 5:
				return "Five";
			case 6:
				return "Six";
			case 7:
				return "Seven";
			case 8:
				return "Eight";
			case 9:
				return "Nine";
		}
		return "";
	}

	public String twoLessThan20(int num) {
		switch (num) {
			case 10:
				return "Ten";
			case 11:
				return "Eleven";
			case 12:
				return "Twelve";
			case 13:
				return "Thirteen";
			case 14:
				return "Fourteen";
			case 15:
				return "Fifteen";
			case 16:
				return "Sixteen";
			case 17:
				return "Seventeen";
			case 18:
				return "Eighteen";
			case 19:
				return "Nineteen";
		}
		return "";
	}

	public String ten(int num) {
		switch (num) {
			case 2:
				return "Twenty";
			case 3:
				return "Thirty";
			case 4:
				return "Forty";
			case 5:
				return "Fifty";
			case 6:
				return "Sixty";
			case 7:
				return "Seventy";
			case 8:
				return "Eighty";
			case 9:
				return "Ninety";
		}
		return "";
	}

	public String two(int num) {
		if (num == 0)
			return "";
		else if (num < 10)
			return one(num);
		else if (num < 20)
			return twoLessThan20(num);
		else {
			int tenner = num / 10;
			int rest = num - tenner * 10;
			if (rest != 0)
				return ten(tenner) + " " + one(rest);
			else
				return ten(tenner);
		}
	}

	public String three(int num) {
		int hundred = num / 100;
		int rest = num - hundred * 100;
		String res = "";
		if (hundred * rest != 0)
			res = one(hundred) + " Hundred " + two(rest);
		else if ((hundred == 0) && (rest != 0))
			res = two(rest);
		else if ((hundred != 0) && (rest == 0))
			res = one(hundred) + " Hundred";
		return res;
	}

	public String numberToWords(int num) {
		if (num == 0)
			return "Zero";

		int billion = num / 1000000000;
		int million = (num - billion * 1000000000) / 1000000;
		int thousand = (num - billion * 1000000000 - million * 1000000) / 1000;
		int rest = num - billion * 1000000000 - million * 1000000 - thousand * 1000;

		String result = "";
		if (billion != 0)
			result = three(billion) + " Billion";
		if (million != 0) {
			if (!result.isEmpty())
				result += " ";
			result += three(million) + " Million";
		}
		if (thousand != 0) {
			if (!result.isEmpty())
				result += " ";
			result += three(thousand) + " Thousand";
		}
		if (rest != 0) {
			if (!result.isEmpty())
				result += " ";
			result += three(rest);
		}
		return result;
	}
}
```



## LC#1567. Maximum Length of Subarray With Positive Product

Given an array of integers `nums`, find the maximum length of a subarray where the product of all its elements is positive.

A subarray of an array is a consecutive sequence of zero or more values taken out of that array.

Return *the maximum length of a subarray with positive product*.

 

**Example 1:**

```
Input: nums = [1,-2,-3,4]
Output: 4
Explanation: The array nums already has a positive product of 24.
```

**Example 2:**

```
Input: nums = [0,1,-2,-3,-4]
Output: 3
Explanation: The longest subarray with positive product is [1,-2,-3] which has a product of 6.
Notice that we cannot include 0 in the subarray since that'll make the product 0 which is not positive.
```

**Example 3:**

```
Input: nums = [-1,-2,-3,0,1]
Output: 2
Explanation: The longest subarray with positive product is [-1,-2] or [-2,-3].
```

 

**Constraints:**

- `1 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`



**Solution**

```
elements      :   9    5    8    2    -6    4    -3    0    2    -5    15    10    -7    9    -2
positive_len  :   1    2    3    4     0    1     7    0    1     0     1     2     5    6     5
negative_len  :   0    0    0    0     5    6     2    0    0     2     3     4     3    4     7
```

Answer : Max(all these positive_len values) => 7

```java
class Solution {
    public int getMaxLen(int[] nums) {
        int positive = 0, negative = 0;    // length of positive and negative results
        int ans = 0;
        for(int x : nums) {
            if(x == 0)  {
                positive = 0;
                negative = 0;
            }
            else if(x > 0) {
                positive++;
                negative = negative == 0 ? 0  : negative+1;
            }
            else {
                int temp = positive;
                positive = negative == 0 ? 0  : negative+1;
                negative = temp+1;
            }
            ans = Math.max(ans, positive);
        }
        return ans;
    }
}
```



## LC#2281. Sum of Total Strength of Wizards

As the ruler of a kingdom, you have an army of wizards at your command.

You are given a **0-indexed** integer array `strength`, where `strength[i]` denotes the strength of the `ith` wizard. For a **contiguous** group of wizards (i.e. the wizards' strengths form a **subarray** of `strength`), the **total strength** is defined as the **product** of the following two values:

- The strength of the **weakest** wizard in the group.
- The **total** of all the individual strengths of the wizards in the group.

Return *the **sum** of the total strengths of **all** contiguous groups of wizards*. Since the answer may be very large, return it **modulo** `109 + 7`.

A **subarray** is a contiguous **non-empty** sequence of elements within an array.

 

**Example 1:**

```
Input: strength = [1,3,1,2]
Output: 44
Explanation: The following are all the contiguous groups of wizards:
- [1] from [1,3,1,2] has a total strength of min([1]) * sum([1]) = 1 * 1 = 1
- [3] from [1,3,1,2] has a total strength of min([3]) * sum([3]) = 3 * 3 = 9
- [1] from [1,3,1,2] has a total strength of min([1]) * sum([1]) = 1 * 1 = 1
- [2] from [1,3,1,2] has a total strength of min([2]) * sum([2]) = 2 * 2 = 4
- [1,3] from [1,3,1,2] has a total strength of min([1,3]) * sum([1,3]) = 1 * 4 = 4
- [3,1] from [1,3,1,2] has a total strength of min([3,1]) * sum([3,1]) = 1 * 4 = 4
- [1,2] from [1,3,1,2] has a total strength of min([1,2]) * sum([1,2]) = 1 * 3 = 3
- [1,3,1] from [1,3,1,2] has a total strength of min([1,3,1]) * sum([1,3,1]) = 1 * 5 = 5
- [3,1,2] from [1,3,1,2] has a total strength of min([3,1,2]) * sum([3,1,2]) = 1 * 6 = 6
- [1,3,1,2] from [1,3,1,2] has a total strength of min([1,3,1,2]) * sum([1,3,1,2]) = 1 * 7 = 7
The sum of all the total strengths is 1 + 9 + 1 + 4 + 4 + 4 + 3 + 5 + 6 + 7 = 44.
```

**Example 2:**

```
Input: strength = [5,4,6]
Output: 213
Explanation: The following are all the contiguous groups of wizards: 
- [5] from [5,4,6] has a total strength of min([5]) * sum([5]) = 5 * 5 = 25
- [4] from [5,4,6] has a total strength of min([4]) * sum([4]) = 4 * 4 = 16
- [6] from [5,4,6] has a total strength of min([6]) * sum([6]) = 6 * 6 = 36
- [5,4] from [5,4,6] has a total strength of min([5,4]) * sum([5,4]) = 4 * 9 = 36
- [4,6] from [5,4,6] has a total strength of min([4,6]) * sum([4,6]) = 4 * 10 = 40
- [5,4,6] from [5,4,6] has a total strength of min([5,4,6]) * sum([5,4,6]) = 4 * 15 = 60
The sum of all the total strengths is 25 + 16 + 36 + 36 + 40 + 60 = 213.
```

 

**Constraints:**

- `1 <= strength.length <= 105`
- `1 <= strength[i] <= 109`



```java

```







# Day #6 (07/03/2022)

## LC#437. Path Sum III

Given the `root` of a binary tree and an integer `targetSum`, return *the number of paths where the sum of the values along the path equals* `targetSum`.

The path does not need to start or end at the root or a leaf, but it must go downwards (i.e., traveling only from parent nodes to child nodes).

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/04/09/pathsum3-1-tree.jpg)

```
Input: root = [10,5,-3,3,2,null,11,3,-2,null,1], targetSum = 8
Output: 3
Explanation: The paths that sum to 8 are shown.
```

**Example 2:**

```
Input: root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
Output: 3
```

**Constraints:**

- The number of nodes in the tree is in the range `[0, 1000]`.
- `-109 <= Node.val <= 109`
- `-1000 <= targetSum <= 1000`

**Solution:**

```java
class Solution {
    
    int count = 0;
    int k;
    Map<Integer, Integer> map = new HashMap<>();
    
    public int pathSum(TreeNode root, int sum) {
        k = sum;
        helper(root, 0);
        return count;
    }
    
    private void helper(TreeNode root, int curSum) {
        if (root == null) {
            return;
        }
        
        curSum += root.val;
        if (curSum == k) {
            count++;
        }
        
        count += map.getOrDefault(curSum - k, 0);
        
        map.put(curSum, map.getOrDefault(curSum, 0) + 1);
        helper(root.left, curSum);
        helper(root.right, curSum);
        map.put(curSum, map.get(curSum) - 1);
    }
}
```



# Day #7 (07/05/2022)

## LC#487. Max Consecutive Ones II

Given a binary array `nums`, return *the maximum number of consecutive* `1`*'s in the array if you can flip at most one* `0`.

**Example 1:**

```
Input: nums = [1,0,1,1,0]
Output: 4
Explanation: Flip the first zero will get the maximum number of consecutive 1s. After flipping, the maximum number of consecutive 1s is 4.
```

**Example 2:**

```
Input: nums = [1,0,1,1,0,1]
Output: 4
```

**Constraints:**

- `1 <= nums.length <= 105`
- `nums[i]` is either `0` or `1`.

**Follow up:** What if the input numbers come in one by one as an infinite stream? In other words, you can't store all numbers coming from the stream as it's too large to hold in memory. Could you solve it efficiently?

**Solution**

```java
class Solution {
    // Sliding Window Solution TC = O(N), SC = O(1)
    public int solve1(int[] nums) {
        int ans = 0;
        int i = 0;
        int j = 0;
        int zeroCount = 0;
        
        while (j < nums.length) {
            zeroCount += nums[j] == 0 ? 1 : 0;
            while (zeroCount == 2) {
                zeroCount -= nums[i] == 0 ? 1 : 0;
                i++;
            }
            ans = Math.max(ans, j - i + 1);
            j++;
        }
        return ans;
    }
    
    // Follow up. Same idea but W queue. TC = O(N), SC = O(K) for flipping K zeros
    public int solve2(int[] nums) {
        int ans = 0;
        int k = 1; // flipping k zeros
        Queue<Integer> zeroIndex = new LinkedList<>();
        for (int i = 0, j = 0; j < nums.length; j++) {
            if (nums[j] == 0) {
                zeroIndex.offer(j);
            }
            if (zeroIndex.size() > k) {
                i = zeroIndex.poll() + 1;
            }
            ans = Math.max(ans, j - i + 1);
        }
        return ans;
    }
}
```





## LC#5. Longest Palindromic Substring

Given a string `s`, return *the longest palindromic substring* in `s`.

**Example 1:**

```
Input: s = "babad"
Output: "bab"
Explanation: "aba" is also a valid answer.
```

**Example 2:**

```
Input: s = "cbbd"
Output: "bb"
```

**Solution**

```java
class Solution {
    public String longestPalindrome(String s) {
        int start = 0;
        int end = 0;
        for (int i = 0; i < s.length(); i++) {
            int len1 = expand(s, i, i);
            int len2 = expand(s, i, i + 1);
            int len = Math.max(len1, len2);
            if (len > end - start) {
                start = i - (len - 1) / 2;
                end = i + len / 2;
            }
        }
        return s.substring(start, end + 1);
    }
    
    private int expand(String s, int left, int right) {
        while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
            left--;
            right++;
        }
        return right - left - 1;
    }
}
// TC = O(N^2)
// SC = O(1)
```



# Day #8 (07/06/2022)

## LC#198. House Robber

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given an integer array `nums` representing the amount of money of each house, return *the maximum amount of money you can rob tonight **without alerting the police***.

**Example 1:**

```
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
```

**Example 2:**

```
Input: nums = [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
Total amount you can rob = 2 + 9 + 1 = 12.
```

**Constraints:**

- `1 <= nums.length <= 100`
- `0 <= nums[i] <= 400`

**Solution:**

```java
class Solution {
    
    // DP solution, TC = O(N), SC = O(N)
    public int solve1(int[] nums) {
        final int N = nums.length;
        
        int[] dp = new int[N];
        dp[0] = nums[0];
        
        for (int i = 1; i < N; i++) {
            dp[i] = Math.max(dp[i - 1], (i >= 2 ? dp[i - 2] : 0) + nums[i]);
        }
        return dp[N - 1];
    }
    
    // Optimized DP solution, TC = O(N), SC = O(1)
    public int solve2(int[] nums) {
        final int N = nums.length;
        int near = nums[0];
        int far = 0;
        
        for (int i = 1; i < N; i++) {
            int cur = Math.max(near, far + nums[i]);
            far = near;
            near = cur;
        }
        return near;
    }
}
```



## LC#213. House Robber II

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are **arranged in a circle.** That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have a security system connected, and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given an integer array `nums` representing the amount of money of each house, return *the maximum amount of money you can rob tonight **without alerting the police***.

**Example 1:**

```
Input: nums = [2,3,2]
Output: 3
Explanation: You cannot rob house 1 (money = 2) and then rob house 3 (money = 2), because they are adjacent houses.
```

**Example 2:**

```
Input: nums = [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
```

**Example 3:**

```
Input: nums = [1,2,3]
Output: 3
```

**Constraints:**

- `1 <= nums.length <= 100`
- `0 <= nums[i] <= 1000`

**Solution**

```java
class Solution {
    // Since House[1] and House[n] are adjacent, they cannot be robbed together. 
    // Therefore, the problem becomes to rob either House[1]-House[n-1] or House[2]-House[n], 
    // depending on which choice offers more money. Now the problem has degenerated to the House Robber, 
    // which is already been solved.
    
    // TC = O(N), SC = O(1)
    public int rob(int[] nums) {
        final int N = nums.length;
        
        if (N == 0) {
            return 0;
        }
        
        if (N == 1) {
            return nums[0];
        }
        
        int max1 = helper(nums, 0, N - 1);
        int max2 = helper(nums, 1, N);
        
        return Math.max(max1, max2);
    }
    
    private int helper(int[] nums, int start, int end) {
        int near = nums[start];
        int far = 0;
        
        for (int i = start + 1; i < end; i++) {
            int cur = Math.max(near, far + nums[i]);
            far = near;
            near = cur;
        }
        
        return near;
    }
}
```



# Day #9 (07/07/2022)

## LC#523. Continuous Subarray Sum

Given an integer array `nums` and an integer `k`, return `true` *if* `nums` *has a continuous subarray of size **at least two** whose elements sum up to a multiple of* `k`*, or* `false` *otherwise*.

An integer `x` is a multiple of `k` if there exists an integer `n` such that `x = n * k`. `0` is **always** a multiple of `k`.

**Example 1:**

```
Input: nums = [23,2,4,6,7], k = 6
Output: true
Explanation: [2, 4] is a continuous subarray of size 2 whose elements sum up to 6.
```

**Example 2:**

```
Input: nums = [23,2,6,4,7], k = 6
Output: true
Explanation: [23, 2, 6, 4, 7] is an continuous subarray of size 5 whose elements sum up to 42.
42 is a multiple of 6 because 42 = 7 * 6 and 7 is an integer.
```

**Example 3:**

```
Input: nums = [23,2,6,4,7], k = 13
Output: false
```

**Constraints:**

- `1 <= nums.length <= 105`
- `0 <= nums[i] <= 109`
- `0 <= sum(nums[i]) <= 231 - 1`
- `1 <= k <= 231 - 1`

```java
class Solution {
    
    // (a + n * x) % x == a % x
    // is the magic of remainder theory. 
    // in case of the array [23,2,6,4,7] the sum is [23,25,31,35,42] and the remainders are [5,1,1,5,0]. 
    // We got remainder 5 at index 0 and at index 3. That means, in between these two indexes we must have added 
    // a number which is multiple of the k
    
    public boolean checkSubarraySum(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(0, -1);
        
        int sum = 0;
        for (int i = 0; i < nums.length; i++) {
            sum = (sum + nums[i]) % k;
            
            if (map.containsKey(sum)) {
                if (i - map.get(sum) > 1) {
                    return true;
                }
            } else {
                map.put(sum, i);
            }
        }
        return false;
        
    }
    
    // Solution if we were to find sum equal to k.
    // Similarly, for nums = [23,2,6,4,7], the prefix sum is [23,25,31,35,42]. 
    // Since sum[3] - sum[1] = 10, we must have added 10 between index 1 and 3. And we can see it's 6 + 4
    public boolean solve(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        int sum = 0;
        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
            if (map.containsKey(sum - k) && (i - map.get(sum - k)) > 1) {
                return true;
            } 
            map.put(sum, i);
        }
        
        return false;
    }
}
```



## LC#787. Cheapest Flights Within K Stops

There are `n` cities connected by some number of flights. You are given an array `flights` where `flights[i] = [fromi, toi, pricei]` indicates that there is a flight from city `fromi` to city `toi` with cost `pricei`.

You are also given three integers `src`, `dst`, and `k`, return ***the cheapest price** from* `src` *to* `dst` *with at most* `k` *stops.* If there is no such route, return `-1`.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2022/03/18/cheapest-flights-within-k-stops-3drawio.png)

```
Input: n = 4, flights = [[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]], src = 0, dst = 3, k = 1
Output: 700
Explanation:
The graph is shown above.
The optimal path with at most 1 stop from city 0 to 3 is marked in red and has cost 100 + 600 = 700.
Note that the path through cities [0,1,2,3] is cheaper but is invalid because it uses 2 stops.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2022/03/18/cheapest-flights-within-k-stops-1drawio.png)

```
Input: n = 3, flights = [[0,1,100],[1,2,100],[0,2,500]], src = 0, dst = 2, k = 1
Output: 200
Explanation:
The graph is shown above.
The optimal path with at most 1 stop from city 0 to 2 is marked in red and has cost 100 + 100 = 200.
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2022/03/18/cheapest-flights-within-k-stops-2drawio.png)

```
Input: n = 3, flights = [[0,1,100],[1,2,100],[0,2,500]], src = 0, dst = 2, k = 0
Output: 500
Explanation:
The graph is shown above.
The optimal path with no stops from city 0 to 2 is marked in red and has cost 500.
```

**Constraints:**

- `1 <= n <= 100`
- `0 <= flights.length <= (n * (n - 1) / 2)`
- `flights[i].length == 3`
- `0 <= fromi, toi < n`
- `fromi != toi`
- `1 <= pricei <= 104`
- There will not be any multiple flights between two cities.
- `0 <= src, dst, k < n`
- `src != dst`

**Solution**

```java
class Solution {
    
    private int[][] matrix;
    private Map<Pair<Integer, Integer>, Long> cache;
    
    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        matrix = new int[n][n];
        cache = new HashMap<>();
        
        for (int[] f : flights) {
            matrix[f[0]][f[1]] = f[2];
        }
        
        long ans = dfs(src, dst, k);
        return ans >= Integer.MAX_VALUE ? -1 : (int)ans;
    }
    
    private long dfs(int src, int dst, int steps) {
        if (src == dst) {
            return 0;
        }
        
        if (steps < 0) {
            return Integer.MAX_VALUE;
        }
        
        Pair<Integer, Integer> key = new Pair<Integer, Integer>(src, steps);
        
        if (cache.containsKey(key)) {
            return cache.get(key);
        }

        long result = Integer.MAX_VALUE;
        for (int d = 0; d < matrix.length; d++) {
            int cost = matrix[src][d];
            if (cost != 0) {
                result = Math.min(dfs(d, dst, steps - 1) + cost, result);
            }
        }
        
        cache.put(key, result);
        return result;
    }
}
```



## LC#403. Frog Jump

A frog is crossing a river. The river is divided into some number of units, and at each unit, there may or may not exist a stone. The frog can jump on a stone, but it must not jump into the water.

Given a list of `stones`' positions (in units) in sorted **ascending order**, determine if the frog can cross the river by landing on the last stone. Initially, the frog is on the first stone and assumes the first jump must be `1` unit.

If the frog's last jump was `k` units, its next jump must be either `k - 1`, `k`, or `k + 1` units. The frog can only jump in the forward direction.

**Example 1:**

```
Input: stones = [0,1,3,5,6,8,12,17]
Output: true
Explanation: The frog can jump to the last stone by jumping 1 unit to the 2nd stone, then 2 units to the 3rd stone, then 2 units to the 4th stone, then 3 units to the 6th stone, 4 units to the 7th stone, and 5 units to the 8th stone.
```

**Example 2:**

```
Input: stones = [0,1,2,3,4,8,9,11]
Output: false
Explanation: There is no way to jump to the last stone as the gap between the 5th and 6th stone is too large.
```

**Constraints:**

- `2 <= stones.length <= 2000`
- `0 <= stones[i] <= 231 - 1`
- `stones[0] == 0`
- `stones` is sorted in a strictly increasing order.

**Solution (DFS)**

```java
class Solution {
    //
    // 0 1 2 3 4 5 6 7  8 9 
    // 0 1 2 3 4 8 9 11
    //   i
    //
    //              0
    //              1
    //        1x    2x    3                        
    //                 4  5x  6x  
    //             4x  5x  6x
    //
    //
    // 0 1 2 3 5 6 8 12 17
    //
    //                     0
    //                     1
    //              1x     2  
    //                  2x 3  
    //                  3x 4x     5
    //                       6    7x         8
    //                    6x 7x   8     10x 11x 12
    //                         9x 10x 11x     15x 16x 17 <-YES
    //
    // TC = O(3^N)
    // SC = O(N)
    
    private Set<Integer> stones;
    private Set<Integer> visited;
    
    public boolean canCross(int[] s) {
        
        if (s[1] != 1) {
            return false;
        }
        
        stones = new HashSet<>();
        for (int stone : s) {
            stones.add(stone);
        }
        
        
        System.out.println(stones);
        visited = new HashSet<>();
        visited.add(0);
        return helper(1, 1, s[s.length - 1]);
    }
    
    
    private boolean helper(int start, int k, int end) {
        // System.out.println(start + ", " + k + ", " + end);
        
        if (start == end) {
            return true;
        }
        
        if (!stones.contains(start)) {
            return false;
        }
        
        if (visited.contains(start)) {
            return false;
        }
        
        visited.add(start);
        
        if (helper(start + k - 1, k - 1, end)) {
            return true;
        }
        
        if (helper(start + k, k, end)) {
            return true;
        }
        
        if (helper(start + k + 1, k + 1, end)) {
            return true;
        }
        
        visited.remove(start);
        return false;
    }
}
```



**Solution (DP)**

```java
class Solution {
    // TC = O(N^2), SC = O(N^2)
    public boolean canCross(int[] stones) {
        
        Map<Integer, Set<Integer>> map = new HashMap<>();
        for (int s : stones) {
            map.put(s, new HashSet<>());
        }
        
        map.get(0).add(0);
        
        for (int i = 0; i < stones.length; i++) {
            for (int k : map.get(stones[i])) {
                if (k - 1 > 0 && map.containsKey(stones[i] + k - 1)) {
                    map.get(stones[i] + k - 1).add(k - 1);
                }
                
                if (k > 0 && map.containsKey(stones[i] + k)) {
                    map.get(stones[i] + k).add(k);
                }
                
                if (k + 1 > 0 && map.containsKey(stones[i] + k + 1)) {
                    map.get(stones[i] + k + 1).add(k + 1);
                }
            }
        }
        return map.get(stones[stones.length - 1]).size() > 0;
    }
}
```





# Day #10 (07/08/2022)

## LC#134. Gas Station

There are `n` gas stations along a circular route, where the amount of gas at the `ith` station is `gas[i]`.

You have a car with an unlimited gas tank and it costs `cost[i]` of gas to travel from the `ith` station to its next `(i + 1)th` station. You begin the journey with an empty tank at one of the gas stations.

Given two integer arrays `gas` and `cost`, return *the starting gas station's index if you can travel around the circuit once in the clockwise direction, otherwise return* `-1`. If there exists a solution, it is **guaranteed** to be **unique**

**Example 1:**

```
Input: gas = [1,2,3,4,5], cost = [3,4,5,1,2]
Output: 3
Explanation:
Start at station 3 (index 3) and fill up with 4 unit of gas. Your tank = 0 + 4 = 4
Travel to station 4. Your tank = 4 - 1 + 5 = 8
Travel to station 0. Your tank = 8 - 2 + 1 = 7
Travel to station 1. Your tank = 7 - 3 + 2 = 6
Travel to station 2. Your tank = 6 - 4 + 3 = 5
Travel to station 3. The cost is 5. Your gas is just enough to travel back to station 3.
Therefore, return 3 as the starting index.
```

**Example 2:**

```
Input: gas = [2,3,4], cost = [3,4,3]
Output: -1
Explanation:
You can't start at station 0 or 1, as there is not enough gas to travel to the next station.
Let's start at station 2 and fill up with 4 unit of gas. Your tank = 0 + 4 = 4
Travel to station 0. Your tank = 4 - 3 + 2 = 3
Travel to station 1. Your tank = 3 - 3 + 3 = 3
You cannot travel back to station 2, as it requires 4 unit of gas but you only have 3.
Therefore, you can't travel around the circuit once no matter where you start.
```

**Constraints:**

- `n == gas.length == cost.length`
- `1 <= n <= 105`
- `0 <= gas[i], cost[i] <= 104`

**Solution:**

```java
class Solution {
    // TC = O(N)
    // SC = O(1)
    public int canCompleteCircuit(int[] gas, int[] cost) {
        final int N = gas.length;
        int start = 0;
        int cur = 0;
        int total = 0;
        
        for (int i = 0; i < N; i++) {
            if (cur < 0) {
                cur = 0;
                start = i;
            }
            
            int diff = gas[i] - cost[i];
            cur += diff;
            total += diff;
            
        }
        return total >= 0 ? start : -1;
    }
}
```



## LC#91. Decode Ways

A message containing letters from `A-Z` can be **encoded** into numbers using the following mapping:

```
'A' -> "1"
'B' -> "2"
...
'Z' -> "26"
```

To **decode** an encoded message, all the digits must be grouped then mapped back into letters using the reverse of the mapping above (there may be multiple ways). For example, `"11106"` can be mapped into:

- `"AAJF"` with the grouping `(1 1 10 6)`
- `"KJF"` with the grouping `(11 10 6)`

Note that the grouping `(1 11 06)` is invalid because `"06"` cannot be mapped into `'F'` since `"6"` is different from `"06"`.

Given a string `s` containing only digits, return *the **number** of ways to **decode** it*.

The test cases are generated so that the answer fits in a **32-bit** integer.

**Example 1:**

```
Input: s = "12"
Output: 2
Explanation: "12" could be decoded as "AB" (1 2) or "L" (12).
```

**Example 2:**

```
Input: s = "226"
Output: 3
Explanation: "226" could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).
```

**Example 3:**

```
Input: s = "06"
Output: 0
Explanation: "06" cannot be mapped to "F" because of the leading zero ("6" is different from "06").
```

**Constraints:**

- `1 <= s.length <= 100`
- `s` contains only digits and may contain leading zero(s).

**Solution:**

```java
// You're already familiar with regular DFS w TC = O(2^N) & SC = O(N)
// Here's a DFS with cached memory, allowing for TC = O(N) & SC = O(N)
class Solution {
    
    private int result;
    private Map<Integer, Integer> cache;

    public int numDecodings(String s) {
        result = 0;
        cache = new HashMap<>();
        return helper(s, 0);
    }

    private int helper(String str, int index) {
        if (cache.containsKey(index)) {
            return cache.get(index);
        }
        
        if (index == str.length()) {
            return 1;
        }
        
        int ans = 0;
        
        // Alt. #1: Process 1 char
        if (str.charAt(index) != '0') {
            ans += helper(str, index + 1);
        }
        
        // Alt. #2: Process 2 char
        if (index + 1 < str.length()) {
            if (str.charAt(index) == '1' || (str.charAt(index) == '2' && str.charAt(index + 1) <= '6')) {
                ans += helper(str, index + 2);
            }
        }
        
        cache.put(index, ans);
        return ans;
    }
}
```



## LC#2115. Find All Possible Recipes from Given Supplies

You have information about `n` different recipes. You are given a string array `recipes` and a 2D string array `ingredients`. The `ith` recipe has the name `recipes[i]`, and you can **create** it if you have **all** the needed ingredients from `ingredients[i]`. Ingredients to a recipe may need to be created from **other** recipes, i.e., `ingredients[i]` may contain a string that is in `recipes`.

You are also given a string array `supplies` containing all the ingredients that you initially have, and you have an infinite supply of all of them.

Return *a list of all the recipes that you can create.* You may return the answer in **any order**.

Note that two recipes may contain each other in their ingredients.

 

**Example 1:**

```
Input: recipes = ["bread"], ingredients = [["yeast","flour"]], supplies = ["yeast","flour","corn"]
Output: ["bread"]
Explanation:
We can create "bread" since we have the ingredients "yeast" and "flour".
```

**Example 2:**

```
Input: recipes = ["bread","sandwich"], ingredients = [["yeast","flour"],["bread","meat"]], supplies = ["yeast","flour","meat"]
Output: ["bread","sandwich"]
Explanation:
We can create "bread" since we have the ingredients "yeast" and "flour".
We can create "sandwich" since we have the ingredient "meat" and can create the ingredient "bread".
```

**Example 3:**

```
Input: recipes = ["bread","sandwich","burger"], ingredients = [["yeast","flour"],["bread","meat"],["sandwich","meat","bread"]], supplies = ["yeast","flour","meat"]
Output: ["bread","sandwich","burger"]
Explanation:
We can create "bread" since we have the ingredients "yeast" and "flour".
We can create "sandwich" since we have the ingredient "meat" and can create the ingredient "bread".
We can create "burger" since we have the ingredient "meat" and can create the ingredients "bread" and "sandwich".
```

 

**Constraints:**

- `n == recipes.length == ingredients.length`
- `1 <= n <= 100`
- `1 <= ingredients[i].length, supplies.length <= 100`
- `1 <= recipes[i].length, ingredients[i][j].length, supplies[k].length <= 10`
- `recipes[i], ingredients[i][j]`, and `supplies[k]` consist only of lowercase English letters.
- All the values of `recipes` and `supplies` combined are unique.
- Each `ingredients[i]` does not contain any duplicate values.

**Solution:**

```java
class Solution {
    
    private Set<String> supply;
    private Map<String, List<String>> recipe;
    private Set<String> visited;  // use visited to prevent stack overflow from chicken & egg situation 
    
    public List<String> findAllRecipes(String[] recipes, List<List<String>> ingredients, String[] supplies){
        List<String> result = new ArrayList<>();
        visited = new HashSet<>();
        
        
        supply = new HashSet<>();
        for (String s : supplies) {
            supply.add(s);
        }
        
        recipe = new HashMap<>();
        for (int i = 0; i < recipes.length; i++) {
            recipe.put(recipes[i], ingredients.get(i));
        }
        
        for (int i = 0; i < recipes.length; i++) {
            if (canMake(recipes[i])) {
                result.add(recipes[i]);
            }
        }
        
        return result;
    }
    
    private boolean canMake(String item) {
        if (supply.contains(item)) {
            return true;
        }
        
        if (visited.contains(item)) {
            return false;
        }
        
        visited.add(item);
        if (recipe.containsKey(item)) {
            for (String requirement : recipe.get(item)) {
                if (!canMake(requirement)) {
                    visited.remove(item);
                    return false;
                }
            }
            visited.remove(item);
            supply.add(item);
            return true;
        }
        visited.remove(item);
        return false;
    }
}
```





# Day #11 (07/11/2022)

## LC#3. Longest Substring Without Repeating Characters

Given a string `s`, find the length of the **longest substring** without repeating characters.

 

**Example 1:**

```
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
```

**Example 2:**

```
Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

**Example 3:**

```
Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

**Constraints:**

- `0 <= s.length <= 5 * 104`
- `s` consists of English letters, digits, symbols and spaces.

**Solution**

```java
class Solution {
    // TC = O(N), SC = O(N)
    public int lengthOfLongestSubstring(String s) {  
        int start = 0;
        int ans = 0;
        Map<Character, Integer> map = new HashMap<>();
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            if (map.containsKey(c)) {
                if (map.get(c) >= start) {
                    start = map.get(c) + 1;
                }
            }
            ans = Math.max(ans, i - start + 1);
            map.put(c, i);
        }
        
        return ans;
    }
}
```



## LC#1. 2Sum

Given an array of integers `nums` and an integer `target`, return *indices of the two numbers such that they add up to `target`*.

You may assume that each input would have ***exactly\* one solution**, and you may not use the *same* element twice.

You can return the answer in any order.

 

**Example 1:**

```
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
```

**Example 2:**

```
Input: nums = [3,2,4], target = 6
Output: [1,2]
```

**Example 3:**

```
Input: nums = [3,3], target = 6
Output: [0,1]
```

**Constraints:**

- `2 <= nums.length <= 104`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`
- **Only one valid answer exists.**

**Solution**

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] ans = new int[2];
        final int N = nums.length;
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < N; i++) {
            int diff = target - nums[i];
            if (map.containsKey(diff)) {
                ans[0] = i;
                ans[1] = map.get(diff);
            }
            map.put(nums[i], i);
        }
        return ans;
    }
}
```



## LC#15. 3Sum

Given an integer array nums, return all the triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, and `j != k`, and `nums[i] + nums[j] + nums[k] == 0`.

Notice that the solution set must not contain duplicate triplets.

 

**Example 1:**

```
Input: nums = [-1,0,1,2,-1,-4]
Output: [[-1,-1,2],[-1,0,1]]
Explanation: 
nums[0] + nums[1] + nums[1] = (-1) + 0 + 1 = 0.
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0.
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0.
The distinct triplets are [-1,0,1] and [-1,-1,2].
Notice that the order of the output and the order of the triplets does not matter.
```

**Example 2:**

```
Input: nums = [0,1,1]
Output: []
Explanation: The only possible triplet does not sum up to 0.
```

**Example 3:**

```
Input: nums = [0,0,0]
Output: [[0,0,0]]
Explanation: The only possible triplet sums up to 0.
```

 

**Constraints:**

- `3 <= nums.length <= 3000`
- `-105 <= nums[i] <= 105`

**Solution**

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums);
        
        for (int i = 0; i < nums.length - 2; i++) {
            
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            
            int j = i + 1;
            int k = nums.length - 1;
            while (j < k) {
                int sum = nums[i] + nums[j] + nums[k];
                if (sum == 0) {
                    result.add(Arrays.asList(nums[i], nums[j], nums[k]));
                    
                    j++;
                    while (j < k && nums[j] == nums[j - 1]) {
                        j++;
                    }
                    
                } else if (sum < 0) {
                    j++;
                } else {
                    k--;
                }
            }
        }
        return result;
    }
}
```





# Day #12 (07/20/2022)

## LC#2178. Maximum Split of Positive Even Integers

You are given an integer `finalSum`. Split it into a sum of a **maximum** number of **unique** positive even integers.

- For example, given `finalSum = 12`, the following splits are **valid** (unique positive even integers summing up to `finalSum`): `(12)`, `(2 + 10)`, `(2 + 4 + 6)`, and `(4 + 8)`. Among them, `(2 + 4 + 6)` contains the maximum number of integers. Note that `finalSum` cannot be split into `(2 + 2 + 4 + 4)` as all the numbers should be unique.

Return *a list of integers that represent a valid split containing a **maximum** number of integers*. If no valid split exists for `finalSum`, return *an **empty** list*. You may return the integers in **any** order.

 

**Example 1:**

```
Input: finalSum = 12
Output: [2,4,6]
Explanation: The following are valid splits: (12), (2 + 10), (2 + 4 + 6), and (4 + 8).
(2 + 4 + 6) has the maximum number of integers, which is 3. Thus, we return [2,4,6].
Note that [2,6,4], [6,2,4], etc. are also accepted.
```

**Example 2:**

```
Input: finalSum = 7
Output: []
Explanation: There are no valid splits for the given finalSum.
Thus, we return an empty array.
```

**Example 3:**

```
Input: finalSum = 28
Output: [6,8,2,12]
Explanation: The following are valid splits: (2 + 26), (6 + 8 + 2 + 12), and (4 + 24). 
(6 + 8 + 2 + 12) has the maximum number of integers, which is 4. Thus, we return [6,8,2,12].
Note that [10,2,4,12], [6,2,4,16], etc. are also accepted.
```

**Constraints:**

- `1 <= finalSum <= 1010`

**Solution**

```java
class Solution {
    public List<Long> maximumEvenSplit(long finalSum) {
        LinkedList<Long> ans = new LinkedList<>();
        if (finalSum % 2 == 0) {
            long i = 2;
            while (i <= finalSum) {
                ans.offer(i);
                finalSum -= i;
                i += 2;
            } 
            ans.offer(finalSum + ans.pollLast());
        }
        return ans;
    }
}
```

**Analysis:**

Assume there are totally `i` iterations, so `2 + 4 + ... + 2 * i = (2 + 2 * i) * i / 2 = i * (i + 1) = i + i`2 `~ finalSum`, get rid of the minor term `i`, we have `i`2 `~ finalSum`, which means `i ~ finalSum`0.5; therefore

Time: `O(n`0.5`)`, where `n = finalSum`, space: `O(1)`- excluding return space.





## LC#2034. Stock Price Fluctuation

You are given a stream of **records** about a particular stock. Each record contains a **timestamp** and the corresponding **price** of the stock at that timestamp.

Unfortunately due to the volatile nature of the stock market, the records do not come in order. Even worse, some records may be incorrect. Another record with the same timestamp may appear later in the stream **correcting** the price of the previous wrong record.

Design an algorithm that:

- **Updates** the price of the stock at a particular timestamp, **correcting** the price from any previous records at the timestamp.
- Finds the **latest price** of the stock based on the current records. The **latest price** is the price at the latest timestamp recorded.
- Finds the **maximum price** the stock has been based on the current records.
- Finds the **minimum price** the stock has been based on the current records.

Implement the `StockPrice` class:

- `StockPrice()` Initializes the object with no price records.
- `void update(int timestamp, int price)` Updates the `price` of the stock at the given `timestamp`.
- `int current()` Returns the **latest price** of the stock.
- `int maximum()` Returns the **maximum price** of the stock.
- `int minimum()` Returns the **minimum price** of the stock.

**Example**

```
StockPrice stockPrice = new StockPrice();
stockPrice.update(1, 10); // Timestamps are [1] with corresponding prices [10].
stockPrice.update(2, 5);  // Timestamps are [1,2] with corresponding prices [10,5].
stockPrice.current();     // return 5, the latest timestamp is 2 with the price being 5.
stockPrice.maximum();     // return 10, the maximum price is 10 at timestamp 1.
stockPrice.update(1, 3);  // The previous timestamp 1 had the wrong price, so it is updated to 3.
                          // Timestamps are [1,2] with corresponding prices [3,5].
stockPrice.maximum();     // return 5, the maximum price is 5 after the correction.
stockPrice.update(4, 2);  // Timestamps are [1,2,4] with corresponding prices [3,5,2].
stockPrice.minimum();     // return 2, the minimum price is 2 at timestamp 4.
```

**Constraints:**

- `1 <= timestamp, price <= 109`
- At most `105` calls will be made **in total** to `update`, `current`, `maximum`, and `minimum`.
- `current`, `maximum`, and `minimum` will be called **only after** `update` has been called **at least once**.

**Solution:**

```java
// TreeMap Solution
// TC = O(NlogN), SC = O(N) 
class StockPrice {
	
    private int latest;
    private Map<Integer, Integer> map;       // time : price
    private TreeMap<Integer, Integer> tree;  // price : frequency
     
    public StockPrice() {
        latest = 0;
        map = new HashMap<>();
        tree = new TreeMap<>();
    }
    
    public void update(int timestamp, int price) {
        latest = Math.max(latest, timestamp);
        if (map.containsKey(timestamp)) {
            int oldPrice = map.get(timestamp);
            tree.put(oldPrice, tree.get(oldPrice) - 1);
            
            if (tree.get(oldPrice) == 0) {
                tree.remove(oldPrice);
            }
        }
        
        map.put(timestamp, price);
        tree.put(price, tree.getOrDefault(price, 0) + 1);
    }
    
    public int current() {
        return map.get(latest);
    }
    
    public int maximum() {
        return tree.lastKey();
    }
    
    public int minimum() {
        return tree.firstKey();
    }
}

// min + max Heap solution
// TC = O(NlogN), SC = O(N)
class StockPrice {

    private int latest;
    private Map<Integer, Integer> map;
    private PriorityQueue<int[]> minHeap, maxHeap;
    
    public StockPrice() {
        latest = 0;
        map = new HashMap<>();
        minHeap = new PriorityQueue<>((a, b) -> a[0] - b[0]);
        maxHeap = new PriorityQueue<>((a, b) -> b[0] - a[0]);
    }
    
    public void update(int timestamp, int price) {
        latest = Math.max(latest, timestamp);
        map.put(timestamp, price);
        minHeap.add(new int[] { price, timestamp });
        maxHeap.add(new int[] { price, timestamp });
    }
    
    public int current() {
        return map.get(latest);
    }
    
    public int maximum() {
        int[] top = maxHeap.peek();
        while (map.get(top[1]) != top[0]) {
            maxHeap.poll();
            top = maxHeap.peek();
        }
        return top[0];
    }
    
    public int minimum() {
        int[] top = minHeap.peek();
        while (map.get(top[1]) != top[0]) {
            minHeap.poll();
            top = minHeap.peek();
        }
        return top[0];
    }
}
```



## LC#2013. Detect Squares

You are given a stream of points on the X-Y plane. Design an algorithm that:

- **Adds** new points from the stream into a data structure. **Duplicate** points are allowed and should be treated as different points.
- Given a query point, **counts** the number of ways to choose three points from the data structure such that the three points and the query point form an **axis-aligned square** with **positive area**.

An **axis-aligned square** is a square whose edges are all the same length and are either parallel or perpendicular to the x-axis and y-axis.

Implement the `DetectSquares` class:

- `DetectSquares()` Initializes the object with an empty data structure.
- `void add(int[] point)` Adds a new point `point = [x, y]` to the data structure.
- `int count(int[] point)` Counts the number of ways to form **axis-aligned squares** with point `point = [x, y]` as described above.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/09/01/image.png)

```
DetectSquares detectSquares = new DetectSquares();
detectSquares.add([3, 10]);
detectSquares.add([11, 2]);
detectSquares.add([3, 2]);
detectSquares.count([11, 10]); // return 1. You can choose:
                               //   - The first, second, and third points
detectSquares.count([14, 8]);  // return 0. The query point cannot form a square with any points in the data structure.
detectSquares.add([11, 2]);    // Adding duplicate points is allowed.
detectSquares.count([11, 10]); // return 2. You can choose:
                               //   - The first, second, and third points
                               //   - The first, third, and fourth points
```



**Solution**

```java
class DetectSquares {
    
    private int[][] pointCount;
    private List<int[]> points;
    
    public DetectSquares() {
        pointCount = new int[1001][1001];
        points = new ArrayList<>();
    }
    
    public void add(int[] point) {
        pointCount[point[0]][point[1]] += 1;
        points.add(point);
    }
    
    public int count(int[] point) {
        
        int ans = 0;
        
        int x1 = point[0];
        int y1 = point[1];
        
        for (int[] p3 : points) {
            int x3 = p3[0];
            int y3 = p3[1];
            if (Math.abs(x1 - x3) == 0 || Math.abs(x1 - x3) != Math.abs(y1 - y3)) {
                continue;
            }
            ans += pointCount[x1][y3] * pointCount[x3][y1];
        }
        return ans;
    }
}
```



# Day #13 (07/21/2022)

## LC#121. Best Time to Buy and Sell Stock

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

You want to maximize your profit by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.

Return *the maximum profit you can achieve from this transaction*. If you cannot achieve any profit, return `0`.

 

**Example 1:**

```
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```

**Example 2:**

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transactions are done and the max profit = 0.
```

**Constraints:**

- `1 <= prices.length <= 105`
- `0 <= prices[i] <= 104`

```java
public class Solution {
    public int maxProfit(int prices[]) {
        int minprice = Integer.MAX_VALUE;
        int maxprofit = 0;
        for (int i = 0; i < prices.length; i++) {
            if (prices[i] < minprice)
                minprice = prices[i];
            else if (prices[i] - minprice > maxprofit)
                maxprofit = prices[i] - minprice;
        }
        return maxprofit;
    }
}
```



## LC#1014. Best Sightseeing Pair

You are given an integer array `values` where values[i] represents the value of the `ith` sightseeing spot. Two sightseeing spots `i` and `j` have a **distance** `j - i` between them.

The score of a pair (`i < j`) of sightseeing spots is `values[i] + values[j] + i - j`: the sum of the values of the sightseeing spots, minus the distance between them.

Return *the maximum score of a pair of sightseeing spots*.

**Example 1:**

```
Input: values = [8,1,5,2,6]
Output: 11
Explanation: i = 0, j = 2, values[i] + values[j] + i - j = 8 + 5 + 0 - 2 = 11
```

**Example 2:**

```
Input: values = [1,2]
Output: 2
```

**Constraints:**

- `2 <= values.length <= 5 * 104`
- `1 <= values[i] <= 1000`

**Solution**

```java
class Solution {
    public int maxScoreSightseeingPair(int[] values) {
        int cur = 0;  // max value seen from the past.
        int ans = 0;
        for (int i = 0; i < values.length; i++) {
            ans = Math.max(ans, cur + values[i]);
            
            // cur decrease by 1 as we go further.
            cur = Math.max(cur, values[i]) - 1;  
        }
        return ans;
    }
}
```





## LC#1937. Maximum Number of Points with Cost

You are given an `m x n` integer matrix `points` (**0-indexed**). Starting with `0` points, you want to **maximize** the number of points you can get from the matrix.

To gain points, you must pick one cell in **each row**. Picking the cell at coordinates `(r, c)` will **add** `points[r][c]` to your score.

However, you will lose points if you pick a cell too far from the cell that you picked in the previous row. For every two adjacent rows `r` and `r + 1` (where `0 <= r < m - 1`), picking cells at coordinates `(r, c1)` and `(r + 1, c2)` will **subtract** `abs(c1 - c2)` from your score.

Return *the **maximum** number of points you can achieve*.

`abs(x)` is defined as:

- `x` for `x >= 0`.
- `-x` for `x < 0`.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/07/12/screenshot-2021-07-12-at-13-40-26-diagram-drawio-diagrams-net.png" alt="img, zoo" style="zoom:50%;" />

```
Input: points = [[1,2,3],[1,5,1],[3,1,1]]
Output: 9
Explanation:
The blue cells denote the optimal cells to pick, which have coordinates (0, 2), (1, 1), and (2, 0).
You add 3 + 5 + 3 = 11 to your score.
However, you must subtract abs(2 - 1) + abs(1 - 0) = 2 from your score.
Your final score is 11 - 2 = 9.
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2021/07/12/screenshot-2021-07-12-at-13-42-14-diagram-drawio-diagrams-net.png" alt="img z" style="zoom:50%;" />

```
Input: points = [[1,5],[2,3],[4,2]]
Output: 11
Explanation:
The blue cells denote the optimal cells to pick, which have coordinates (0, 1), (1, 1), and (2, 0).
You add 5 + 3 + 4 = 12 to your score.
However, you must subtract abs(1 - 1) + abs(1 - 0) = 1 from your score.
Your final score is 12 - 1 = 11.
```

**Constraints:**

- `m == points.length`
- `n == points[r].length`
- `1 <= m, n <= 105`
- `1 <= m * n <= 105`
- `0 <= points[r][c] <= 105`

**Solution**

```java
class Solution {
    public long maxPoints(int[][] points) {
        
        final int M = points.length;
        final int N = points[0].length;
        
        // Avoiding N-Queen / DFS / brute force based O(M^N) solution, we do:
        //
        // L[i] = largest value in C[:i+1] = max(L[i - 1] - 1, C[i]) or C[i] if i == 0
        // R[i] = largest value in C[i:] = max(R[i + 1] - 1, C[i]) or C[i] if i == N - 1
        // C[i] = max(L[i], R[i]) + P[next layer][i]
        // keep iterating above process and, for example 1, we get: 
        //
        // P0 1 2 3
        // L  1 2 3 
        // R  1 2 3
        // P1 2 7 4
        // L  2 7 6
        // R  6 7 4
        // P2 9 8 7
        // 
        // And take max at last layer to get final result. 
        // TC = O(MN), SC = O(N)

        long[] cur = new long[N];
        for (int i = 0; i < N; i++) {
            cur[i] = points[0][i];
        }
        
        for (int i = 1; i < M; i++) {
            
            long[] left = new long[N];
            left[0] = cur[0];
            for (int j = 1; j < N; j++) {
                left[j] = Math.max(left[j - 1] - 1, cur[j]);
            }
            
            long[] right = new long[N];
            right[N - 1] = cur[N - 1];
            for (int j = N - 2; j >= 0; j--) {
                right[j] = Math.max(right[j + 1] - 1, cur[j]);
            }
            
            
            for (int j = 0; j < N; j++) {
                cur[j] = points[i][j] + Math.max(left[j], right[j]);
            }
        }
        
        long ans = 0;
        for (int i = 0; i < N; i++) {
            ans = Math.max(ans, cur[i]);
        }
        return ans;
    }
}
```



## LC#1146. Snapshot Array

Implement a SnapshotArray that supports the following interface:

- `SnapshotArray(int length)` initializes an array-like data structure with the given length. **Initially, each element equals 0**.
- `void set(index, val)` sets the element at the given `index` to be equal to `val`.
- `int snap()` takes a snapshot of the array and returns the `snap_id`: the total number of times we called `snap()` minus `1`.
- `int get(index, snap_id)` returns the value at the given `index`, at the time we took the snapshot with the given `snap_id`

**Example 1:**

```
snapshotArray snapshotArr = new SnapshotArray(3); // set the length to be 3
snapshotArr.set(0,5);  // Set array[0] = 5
snapshotArr.snap();  // Take a snapshot, return snap_id = 0
snapshotArr.set(0,6);
snapshotArr.get(0,0);  // Get the value of array[0] with snap_id = 0, return 5
```

**Constraints:**

- `1 <= length <= 50000`
- At most `50000` calls will be made to `set`, `snap`, and `get`.
- `0 <= index < length`
- `0 <= snap_id < `(the total number of times we call `snap()`)
- `0 <= val <= 10^9`

**Solution:**

```java
class SnapshotArray {

    private TreeMap<Integer, Integer>[] M;
    private int id;
    
    public SnapshotArray(int length) {
        id = 0;
        M = new TreeMap[length];
        for (int i = 0; i < length; i++) {
            M[i] = new TreeMap<Integer, Integer>();
            M[i].put(0, 0);
        }
    }
    
    public void set(int index, int val) {
        M[index].put(id, val);
    }
    
    public int snap() {
        return id++;
    }
    
    public int get(int index, int snap_id) {
        // use largest key smaller than snap_id if value has not changed since last snap.
        return M[index].floorEntry(snap_id).getValue();
    }
}
```





# Day #14 (07/22/2022)

## READ THE FUCKING QUESTION, EDDY !!!

## LC#900. RLE Iterator

We can use run-length encoding (i.e., **RLE**) to encode a sequence of integers. In a run-length encoded array of even length `encoding` (**0-indexed**), for all even `i`, `encoding[i]` tells us the number of times that the non-negative integer value `encoding[i + 1]` is repeated in the sequence.

- For example, the sequence `arr = [8,8,8,5,5]` can be encoded to be `encoding = [3,8,2,5]`. `encoding = [3,8,0,9,2,5]` and `encoding = [2,8,1,8,2,5]` are also valid **RLE** of `arr`.

Given a run-length encoded array, design an iterator that iterates through it.

Implement the `RLEIterator` class:

- `RLEIterator(int[] encoded)` Initializes the object with the encoded array `encoded`.
- `int next(int n)` Exhausts the next `n` elements and returns the last element exhausted in this way. If there is no element left to exhaust, return `-1` instead.

**Example 1:**

```java
RLEIterator r = new RLEIterator([3, 8, 0, 9, 2, 5]); // This maps to the sequence [8,8,8,5,5].
r.next(2); // exhausts 2 terms of the sequence, returning 8. The remaining sequence is now [8, 5, 5].
r.next(1); // exhausts 1 term of the sequence, returning 8. The remaining sequence is now [5, 5].
r.next(1); // exhausts 1 term of the sequence, returning 5. The remaining sequence is now [5].
r.next(2); // exhausts 2 terms, returning -1. This is because the first term exhausted was 5, but the second term did not exist. Since the last term exhausted does not exist, we return -1.
```

**Constraints:**

- `2 <= encoding.length <= 1000`
- `encoding.length` is even.
- `0 <= encoding[i] <= 109`
- `1 <= n <= 109`
- At most `1000` calls will be made to `next`.

**Solution:**

```java
class RLEIterator {
    //
    //   0 1 2 3 4 5 6 7 8
    // A 1 8 0 9 2 5
    //   i j
    //
    // n = 1
    //
    // while (i < |A| && A[i] <= n)
    //     n -= A[i]
    //     A[i] = 0
    //     i += 2    
    //    
    // if i == |A|
    //     return -1
    // else
    //     A[i] -= n
    //     return A[i + 1]
    //
    // TC = O(|encoding|)
    // SC = O(1)
    //

    private int[] encoding;
    private int index;
    
    public RLEIterator(int[] encoding) {
        this.encoding = encoding;
        this.index = 0;
    }
    
    public int next(int n) {
        while (index < encoding.length && encoding[index] < n) {
            n -= encoding[index];
            encoding[index] = 0;
            index += 2;
        }
        if (index == encoding.length) {
            return -1;
        }
        encoding[index] -= n;
        return encoding[index + 1];
    }
}
```



## LC#146. LRU Cache

Design a data structure that follows the constraints of a **[Least Recently Used (LRU) cache](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU)**.

Implement the `LRUCache` class:

- `LRUCache(int capacity)` Initialize the LRU cache with **positive** size `capacity`.
- `int get(int key)` Return the value of the `key` if the key exists, otherwise return `-1`.
- `void put(int key, int value)` Update the value of the `key` if the `key` exists. Otherwise, add the `key-value` pair to the cache. If the number of keys exceeds the `capacity` from this operation, **evict** the least recently used key.

The functions `get` and `put` must each run in `O(1)` average time complexity.

**Example 1:**

```
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // cache is {1=1}
lRUCache.put(2, 2); // cache is {1=1, 2=2}
lRUCache.get(1);    // return 1
lRUCache.put(3, 3); // LRU key was 2, evicts key 2, cache is {1=1, 3=3}
lRUCache.get(2);    // returns -1 (not found)
lRUCache.put(4, 4); // LRU key was 1, evicts key 1, cache is {4=4, 3=3}
lRUCache.get(1);    // return -1 (not found)
lRUCache.get(3);    // return 3
lRUCache.get(4);    // return 4
```

**Constraints:**

- `1 <= capacity <= 3000`
- `0 <= key <= 10^4`
- `0 <= value <= 10^5`
- At most 2 * 10^5 calls will be made to `get` and `put`.

**Solution:**

```java
class LRUCache {
    
	// TC = O(1) for all methods, SC = O(capacity)
    // Using LinkedHashSet is not the purpose of this problem
    // Think about the interaction between map and LL
    // Think about why we use double-LL instead of regular LL
    
    private int capacity;
    private Map<Integer, Node> map;  // key : corresponding node
    private Node head;
    private Node tail;
    
    public LRUCache(int c) {
        capacity = c;
        map = new HashMap<>();
        head = new Node(0,0);
        tail = new Node(0,0);
        head.next = tail;
        tail.next = head;
    }
    
    private void insert(Node node) {
        head.next.last = node;
        node.last = head;
        node.next = head.next;
        head.next = node;
    }
    
    private void remove(Node node) {
        node.next.last = node.last;
        node.last.next = node.next;
    }

    private Node removeTail() {
        Node removed = tail.last;
        remove(removed);
        return removed;
    }
    
    private void moveToHead(int key) {
        Node node = map.get(key);
        
        node.last.next = node.next;
        node.next.last = node.last;
        
        insert(node);
    }
    
    
    public int get(int key) {
        if (!map.containsKey(key)) {
            return -1;
        }
        moveToHead(key);
        return map.get(key).value;
    }
    
    public void put(int key, int value) {
        if (map.containsKey(key)) {
            map.get(key).value = value;
            moveToHead(key);
            return;
        }
        
        if (map.size() == capacity) {
            Node oldest = removeTail();
            map.remove(oldest.key);
        }
        
        Node node = new Node(key, value);
        map.put(key, node);
        insert(node);
    }
}

class Node {
    public int key;
    public int value;
    public Node next;
    public Node last;
    public Node(int k, int v) {
        key = k;
        value = v;
    }
}
```



## LC#1423. Maximum Points You Can Obtain from Cards

There are several cards **arranged in a row**, and each card has an associated number of points. The points are given in the integer array `cardPoints`.

In one step, you can take one card from the beginning or from the end of the row. You have to take exactly `k` cards.

Your score is the sum of the points of the cards you have taken.

Given the integer array `cardPoints` and the integer `k`, return the *maximum score* you can obtain.

**Example 1:**

```
Input: cardPoints = [1,2,3,4,5,6,1], k = 3
Output: 12
Explanation: After the first step, your score will always be 1. However, choosing the rightmost card first will maximize your total score. The optimal strategy is to take the three cards on the right, giving a final score of 1 + 6 + 5 = 12.
```

**Example 2:**

```
Input: cardPoints = [2,2,2], k = 2
Output: 4
Explanation: Regardless of which two cards you take, your score will always be 4.
```

**Example 3:**

```
Input: cardPoints = [9,7,7,9,7,7,9], k = 7
Output: 55
Explanation: You have to take all the cards. Your score is the sum of points of all cards.
```

**Constraints:**

- `1 <= cardPoints.length <= 105`
- `1 <= cardPoints[i] <= 104`
- `1 <= k <= cardPoints.length`

**Solution:**

```java
class Solution {
    // #1. DFS / Brute Force Solution: TC = O(2^k), SC = O(k) 
    // #2. Sliding Window: TC = O(k), SC = O(1)
    //
    // 0  1  2  3  4  5  6  7  8  9 
    // 96 90 41 82 39 74 64 50 30 
    //                      L  R
    // Beware of corner case at beginning & end
    
    public int maxScore(int[] cardPoints, int k) {
        
        final int N = cardPoints.length;
        
        int left = 0;
        int leftSum = 0;
        
        int right = N - k;
        int rightSum = 0;
        for (int i = right; i < N; i++) {
            rightSum += cardPoints[i];
        }
        
        if (right == left) {
            return rightSum;   // CORNER CASE (BEGINNING)
        } 
        
        int ans = Integer.MIN_VALUE;
        while (right < N) {
            ans = Math.max(leftSum + rightSum, ans);
            rightSum -= cardPoints[right++];
            leftSum += cardPoints[left++];
        }
        
        ans = Math.max(leftSum, ans);   // CORNER CASE (END)
        return ans;
    }
}
```



# Day #15 (07/24/2022)

## **Hash Table**

```java
import java.util.*;

public class Hashing {
	public static void main(String[] args) {
		// Node a = new Node("eddy", 1, 2);
		// Node b = new Node("berry", 3, 4);
		// Node c = new Node("eddy", 1, 2);
		
		// Set<Node> set = new HashSet<>();
		// set.add(a);
		// set.add(b);
		// set.add(c);
		// System.out.println(set);

		Map map = new Map();
		map.put("eddy", 24);
		map.put("jack", 21);
		map.put("George", 21);
		System.out.println(map);
		System.out.println(map.contains("eddy"));
		System.out.println(map.get("eddy"));
		// System.out.println(map.size());
		
		map.delete("eddy");
		System.out.println(map);
		System.out.println(map.contains("eddy"));
		// System.out.println(map.size());
	}
}

class Node {

	public String name;
	public int x;
	public int y;

	public Node(String name, int x, int y) {
		this.name = new String(name);
		this.x = x;
		this.y = y;
	}

	@Override
	public String toString() {
		return name + ": (" + x + ", " + y + ")";
	}

	@Override
	public boolean equals(Object other) {
		if (!(other instanceof Node)) {
			return false;
		}
		Node otherNode = (Node) other;
		return (this.x == otherNode.x) && (this.y == otherNode.y) && (this.name.equals(otherNode.name));
	}

	@Override
	public int hashCode() {
		return toString().hashCode();
	}
}

class Map {
	private String[] keys;
	private int[] values;
	private int size;  // NOT capacity
	private int capacity;

	public Map() {
		capacity = 20;
		size = 0;

		keys = new String[capacity];
		values = new int[capacity];
	}

	// TODO: 
	//     #1. Implement custom hashcode function
	//     #2. Implement extend capacity after 75% used
	//     #3. Use Integer as value type so we can return Null

	private int getIndex(String k) {
		return k.hashCode() % capacity;
	}

	public void put(String k, int v) {
		int index = getIndex(k);

		if (keys[index] != null && !keys[index].equals(k)) {
			// Handle Hash Collision
			int i = index + 1;
			while (i != index && keys[i] != null) {
				i = i == capacity ? 0 : i + 1;
			}
			index = i;
		}

		if (keys[index] == null) {
			// Insert New Key If Necessary
			keys[index] = new String(k);
			size++;
		}

		values[index] = v;
	}

	public int get(String k) {
		if (!contains(k)) {
			// throw exception, or use Integer and return Null
			return 0;
		}
		int index = getIndex(k);

		while (!keys[index].equals(k)) {
			// Handle Hash Collision
			index = index == capacity ? 0 : index + 1;
		}

		return values[index];
	}

	public void delete(String k) {
		if (!contains(k)) {
			return;
		}
		int index = getIndex(k);
		keys[index] = null;
		values[index] = 0;
		size--;
	}

	public boolean contains(String k) {
		int index = getIndex(k);
		return keys[index] != null && keys[index].equals(k);
	}

	public int size() {
		return size;
	}

	@Override
	public String toString() {
		StringBuilder sb = new StringBuilder();
		sb.append("{ ");
		for (int i = 0; i < capacity; i++) {
			
			String k = keys[i];
			int v = values[i];
			
			if (k == null) {
				continue;
			}		
			sb.append("[" + i + "]" + k + ":" + v + ", ");
		}
		sb.append(" }");
		return sb.toString();
	}
}
```



# Day #16 (07/25/2022)

## LC#56. Merge Intervals

Given an array of `intervals` where `intervals[i] = [starti, endi]`, merge all overlapping intervals, and return *an array of the non-overlapping intervals that cover all the intervals in the input*.

 

**Example 1:**

```
Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlap, merge them into [1,6].
```

**Example 2:**

```
Input: intervals = [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping.
```

 

**Constraints:**

- `1 <= intervals.length <= 10^4`
- `intervals[i].length == 2`
- `0 <= starti <= endi <= 10^4`



**Solution (Eddy / Correct )**

```java
class Solution {
    // TC = O(NlogN), SC = O(N) 
    public int[][] merge(int[][] A) {
        Arrays.sort(A, new MyComparator());
        List<int[]> result = new ArrayList<>();
        
        int i = 0;
        while (i < A.length) {
            int[] cur = A[i];
            while (i < A.length) {
                if (cur[1] < A[i][0]) {
                    break;
                }
                cur[0] = Math.min(A[i][0], cur[0]);
                cur[1] = Math.max(A[i][1], cur[1]);
                i++;
            }
            result.add(new int[]{cur[0], cur[1]});
        }
        
        int[][] ans = new int[result.size()][2];
        result.toArray(ans);
        return ans;
    }
}

class MyComparator implements Comparator<int[]> {
    @Override
    public int compare(int[] a, int[] b) {
        return a[0] - b[0];
    }
}
```



**Solution (LC)**

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
        LinkedList<int[]> merged = new LinkedList<>();
        for (int[] interval : intervals) {
            if (merged.isEmpty() || merged.getLast()[1] < interval[0]) {
                merged.add(interval);
            } else {
                merged.getLast()[1] = Math.max(merged.getLast()[1], interval[1]);
            }
        }
        return merged.toArray(new int[merged.size()][]);
    }
}
```





## LC#253. Meeting Rooms II

Given an array of meeting time intervals `intervals` where `intervals[i] = [starti, endi]`, return *the minimum number of conference rooms required*.

**Example 1:**

```
Input: intervals = [[0,30],[5,10],[15,20]]
Output: 2
```

**Example 2:**

```
Input: intervals = [[7,10],[2,4]]
Output: 1
```

**Constraints:**

- `1 <= intervals.length <= 104`
- `0 <= starti < endi <= 106`

**Solution**

```java
class Solution {
    // Eddy's original thought: create class meet room, add meetings into each instance
    // and create new instance if conflict exist. Return # of rooms we end up.
    // 
    // The Solution below is essentially that, but 
    // w/o custom class and w heap instead of linear scan to check for conflict (N^2 -> NlogN)
    // 
    // TC = O(NlogN), SC = O(N)
    public int minMeetingRooms(int[][] A) {
        final int N = A.length;
        
        if (N == 0) {
            return 0;
        }
        
        PriorityQueue<Integer> heap = new PriorityQueue<>(N, (a, b) -> a - b);
        Arrays.sort(A, (a, b) -> a[0] - b[0]);
        
        heap.add(A[0][1]);
        
        for (int i = 1; i < A.length; i++) {
            if (A[i][0] >= heap.peek()) {
                heap.poll();
            }
            heap.add(A[i][1]);
        }
        
        return heap.size();
    }
}
```



# Day #17 (08/08/2022)

LC#735 O,   LC#658 O,     LC# 1352 Almost

## LC#1352. Product of the Last K Numbers

Design an algorithm that accepts a stream of integers and retrieves the product of the last `k` integers of the stream.

Implement the `ProductOfNumbers` class:

- `ProductOfNumbers()` Initializes the object with an empty stream.
- `void add(int num)` Appends the integer `num` to the stream.
- `int getProduct(int k)` Returns the product of the last `k` numbers in the current list. You can assume that always the current list has at least `k` numbers.

The test cases are generated so that, at any time, the product of any contiguous sequence of numbers will fit into a single 32-bit integer without overflowing.

**Example:**

```java
ProductOfNumbers productOfNumbers = new ProductOfNumbers();
productOfNumbers.add(3);        // [3]
productOfNumbers.add(0);        // [3,0]
productOfNumbers.add(2);        // [3,0,2]
productOfNumbers.add(5);        // [3,0,2,5]
productOfNumbers.add(4);        // [3,0,2,5,4]
productOfNumbers.getProduct(2); // return 20. The product of the last 2 numbers is 5 * 4 = 20
productOfNumbers.getProduct(3); // return 40. The product of the last 3 numbers is 2 * 5 * 4 = 40
productOfNumbers.getProduct(4); // return 0. The product of the last 4 numbers is 0 * 2 * 5 * 4 = 0
productOfNumbers.add(8);        // [3,0,2,5,4,8]
productOfNumbers.getProduct(2); // return 32. The product of the last 2 numbers is 4 * 8 = 32 
```



**Solution**

```java
class ProductOfNumbers {

    private List<Integer> memory;
    
    public ProductOfNumbers() {
        memory = new ArrayList<>();
        memory.add(1);
    }
    
    public void add(int num) {
        if (num == 0) {
            memory = new ArrayList<>();
            memory.add(1);
        } else {
            int last = memory.get(memory.size() - 1);
            memory.add(last * num);
        }
        
    }
    
    public int getProduct(int k) {
        if (k >= memory.size()) {
            return 0;
        }
        int last = memory.get(memory.size() - 1);
        int first = memory.get(memory.size() - k - 1);
        return last / first;
    }
}
```



# Day #18 (08/13/2022)

## Java Big Integer

```java
import java.math.BigInteger;

public class Runnable {
	public static void main(String[] args) {
		
		final BigInteger M = new BigInteger("10").pow(9).add(new BigInteger("7"));

		BigInteger a = new BigInteger("2");
        
		a = a.pow(500).mod(M);
        
		System.out.println(a);
	}
}
```



## LC#1220. Count Vowels Permutation

Given an integer `n`, your task is to count how many strings of length `n` can be formed under the following rules:

- Each character is a lower case vowel (`'a'`, `'e'`, `'i'`, `'o'`, `'u'`)
- Each vowel `'a'` may only be followed by an `'e'`.
- Each vowel `'e'` may only be followed by an `'a'` or an `'i'`.
- Each vowel `'i'` **may not** be followed by another `'i'`.
- Each vowel `'o'` may only be followed by an `'i'` or a `'u'`.
- Each vowel `'u'` may only be followed by an `'a'.`

Since the answer may be too large, return it modulo `10^9 + 7.`

**Example 1:**

```
Input: n = 1
Output: 5
Explanation: All possible strings are: "a", "e", "i" , "o" and "u".
```

**Example 2:**

```
Input: n = 2
Output: 10
Explanation: All possible strings are: "ae", "ea", "ei", "ia", "ie", "io", "iu", "oi", "ou" and "ua".
```

**Example 3:** 

```
Input: n = 5
Output: 68
```

**Constraints:**

- `1 <= n <= 2 * 10^4`

**Solution**

```java
class Solution {
    // Optimized Bottom-Up DP, TC = O(N), SC = O(1)
    public int countVowelPermutation(int n) {
        long aCount = 1, eCount = 1, iCount = 1, oCount = 1, uCount = 1;
        int MOD = 1000000007;

        for (int i = 1; i < n; i++) {
            long aCountNew = (eCount + iCount + uCount) % MOD;
            long eCountNew = (aCount + iCount) % MOD;
            long iCountNew = (eCount + oCount) % MOD;
            long oCountNew = (iCount) % MOD;
            long uCountNew = (iCount + oCount) % MOD;
            aCount = aCountNew;
            eCount = eCountNew;
            iCount = iCountNew;
            oCount = oCountNew;
            uCount = uCountNew;
        }
        long result = (aCount + eCount + iCount + oCount + uCount)  % MOD;
        return (int)result;
    }
}
```





# Day #19 (08/14/2022)

## LC#118. Pascal's Triangle

Given an integer `numRows`, return the first numRows of **Pascal's triangle**.

In **Pascal's triangle**, each number is the sum of the two numbers directly above it as shown:

![img](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)

 

**Example 1:**

```
Input: numRows = 5
Output: [[1],[1,1],[1,2,1],[1,3,3,1],[1,4,6,4,1]]
```

**Example 2:**

```
Input: numRows = 1
Output: [[1]]
```

**Constraints:**

- `1 <= numRows <= 30`

**Solution:**

```java
class Solution {
    // TC = O(N^2), SC = O(N)
    public List<List<Integer>> generate(int n) {
        List<List<Integer>> ans = new ArrayList<>();
        
        ans.add(new ArrayList<>());
        ans.get(0).add(1);
        
        for (int i = 1; i < n; i++) {
            List<Integer> row = new ArrayList<>();
            List<Integer> pre = ans.get(i - 1);
            
            row.add(1);
            
            for (int j = 1; j < i; j++) {
                row.add(pre.get(j - 1) + pre.get(j));
            }
            
            row.add(1);
            ans.add(row);
        }
        
        return ans;
    }
}
```



## LC#119. Pascal's Triangle II

Given an integer `rowIndex`, return the `rowIndexth` (**0-indexed**) row of the **Pascal's triangle**.

In **Pascal's triangle**, each number is the sum of the two numbers directly above it as shown:

![img](https://upload.wikimedia.org/wikipedia/commons/0/0d/PascalTriangleAnimated2.gif)

 

**Example 1:**

```
Input: rowIndex = 3
Output: [1,3,3,1]
```

**Example 2:**

```
Input: rowIndex = 0
Output: [1]
```

**Example 3:**

```
Input: rowIndex = 1
Output: [1,1]
```

**Constraints:**

- `0 <= rowIndex <= 33`

**Solution:**

```java
class Solution {
    // TC = O(N^2), SC = O(N)
    public List<Integer> getRow(int n) {
        Integer[] ans = new Integer[n + 1];
        
        Arrays.fill(ans, new Integer(0));
        ans[0] = new Integer(1);
        
        for (int i = 1; i < ans.length; i++) {
            for (int j = i; j > 0; j--) {
                ans[j] = ans[j - 1] + ans[j];
            }
        }
        return Arrays.asList(ans);
    }
}
```



## LC#2222. Number of Ways to Select Buildings

You are given a **0-indexed** binary string `s` which represents the types of buildings along a street where:

- `s[i] = '0'` denotes that the `ith` building is an office and
- `s[i] = '1'` denotes that the `ith` building is a restaurant.

As a city official, you would like to **select** 3 buildings for random inspection. However, to ensure variety, **no two consecutive** buildings out of the **selected** buildings can be of the same type.

- For example, given `s = "0**0**1**1**0**1**"`, we cannot select the `1st`, `3rd`, and `5th` buildings as that would form `"0**11**"` which is **not** allowed due to having two consecutive buildings of the same type.

Return *the **number of valid ways** to select 3 buildings.*

**Example 1:**

```
Input: s = "001101"
Output: 6
Explanation: 
The following sets of indices selected are valid:
- [0,2,4] from "001101" forms "010"
- [0,3,4] from "001101" forms "010"
- [1,2,4] from "001101" forms "010"
- [1,3,4] from "001101" forms "010"
- [2,4,5] from "001101" forms "101"
- [3,4,5] from "001101" forms "101"
No other selection is valid. Thus, there are 6 total ways.
```

**Example 2:**

```
Input: s = "11100"
Output: 0
Explanation: It can be shown that there are no valid selections.
```

**Constraints:**

- `3 <= s.length <= 105`
- `s[i]` is either `'0'` or `'1'`.

**Solution:**

```python
def func(a):
	ans = 0

	ones = 0
	zeros = 0

	for i in range(len(a)):
		if a[i] == '1':
			ones += 1
		else:
			zeros += 1

	ones_ = 0
	zeros_ = 0

	for i in range(len(a)):
		if a[i] == '1':
			ans += zeros * zeros_
			ones -= 1
			ones_ += 1
		else:
			ans += ones * ones_
			zeros -= 1
			zeros_ += 1

	return ans

print(func(['0', '0', '1', '1', '0', '1']))
```



## LC#937. Reorder Data in Log Files

You are given an array of `logs`. Each log is a space-delimited string of words, where the first word is the **identifier**.

There are two types of logs:

- **Letter-logs**: All words (except the identifier) consist of lowercase English letters.
- **Digit-logs**: All words (except the identifier) consist of digits.

Reorder these logs so that:

1. The **letter-logs** come before all **digit-logs**.
2. The **letter-logs** are sorted lexicographically by their contents. If their contents are the same, then sort them lexicographically by their identifiers.
3. The **digit-logs** maintain their relative ordering.

Return *the final order of the logs*.

**Example 1:**

```
Input: logs = ["dig1 8 1 5 1","let1 art can","dig2 3 6","let2 own kit dig","let3 art zero"]
Output: ["let1 art can","let3 art zero","let2 own kit dig","dig1 8 1 5 1","dig2 3 6"]
Explanation:
The letter-log contents are all different, so their ordering is "art can", "art zero", "own kit dig".
The digit-logs have a relative order of "dig1 8 1 5 1", "dig2 3 6".
```

**Example 2:**

```
Input: logs = ["a1 9 2 3 1","g1 act car","zo4 4 7","ab1 off key dog","a8 act zoo"]
Output: ["g1 act car","a8 act zoo","ab1 off key dog","a1 9 2 3 1","zo4 4 7"]
```

**Constraints:**

- `1 <= logs.length <= 100`
- `3 <= logs[i].length <= 100`
- All the tokens of `logs[i]` are separated by a **single** space.
- `logs[i]` is guaranteed to have an identifier and at least one word after the identifier.

**Solution:**

```java
class Solution {
    public String[] reorderLogFiles(String[] logs) {
        
        Arrays.sort(logs, (s1, s2) -> {
            String[] split1 = s1.split(" ", 2);
            String[] split2 = s2.split(" ", 2);
            
            boolean isDigit1 = Character.isDigit(split1[1].charAt(0));
            boolean isDigit2 = Character.isDigit(split2[1].charAt(0));
            
            if (isDigit1 && isDigit2) {
                return 0;
            } else if (isDigit1 || isDigit2) {
                return isDigit1 ? 1 : -1;
            } else {
                int comp = split1[1].compareTo(split2[1]);
                if (comp == 0) {
                    return split1[0].compareTo(split2[0]);
                }
                return comp;
            }
        });
        
        return logs;
    }
}
```



# Day #20 (08/15/2022)

## LC#785. Is Graph Bipartite?

There is an **undirected** graph with `n` nodes, where each node is numbered between `0` and `n - 1`. You are given a 2D array `graph`, where `graph[u]` is an array of nodes that node `u` is adjacent to. More formally, for each `v` in `graph[u]`, there is an undirected edge between node `u` and node `v`. The graph has the following properties:

- There are no self-edges (`graph[u]` does not contain `u`).
- There are no parallel edges (`graph[u]` does not contain duplicate values).
- If `v` is in `graph[u]`, then `u` is in `graph[v]` (the graph is undirected).
- The graph may not be connected, meaning there may be two nodes `u` and `v` such that there is no path between them.

A graph is **bipartite** if the nodes can be partitioned into two independent sets `A` and `B` such that **every** edge in the graph connects a node in set `A` and a node in set `B`.

Return `true` *if and only if it is **bipartite***.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/21/bi2.jpg)

```
Input: graph = [[1,2,3],[0,2],[0,1,3],[0,2]]
Output: false
Explanation: There is no way to partition the nodes into two independent sets such that every edge connects a node in one and a node in the other.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/10/21/bi1.jpg)

```
Input: graph = [[1,3],[0,2],[1,3],[0,2]]
Output: true
Explanation: We can partition the nodes into two sets: {0, 2} and {1, 3}.
```

 

**Constraints:**

- `graph.length == n`
- `1 <= n <= 100`
- `0 <= graph[u].length < n`
- `0 <= graph[u][i] <= n - 1`
- `graph[u]` does not contain `u`.
- All the values of `graph[u]` are **unique**.
- If `graph[u]` contains `v`, then `graph[v]` contains `u`.



**Solution:**

```java
class Solution {
    public boolean isBipartite(int[][] graph) {
        final int n = graph.length;
        int[] color = new int[n];
        Arrays.fill(color, -1);

        for (int i = 0; i < n; i++) {
            if (color[i] == -1) {
                Queue<Integer> queue = new LinkedList<>();
                queue.offer(i);
                color[i] = 0;

                while (!queue.isEmpty()) {
                    Integer node = queue.poll();
                    for (int nei: graph[node]) {
                        if (color[nei] == -1) {
                            queue.offer(nei);
                            color[nei] = color[node] == 0 ? 1 : 0;
                        } else if (color[nei] == color[node]) {
                            return false;
                        }
                    }
                }
            }
        }
        return true;
    }
}
```





# Day #21 (08/19/2022)



## LC#2214. Minimum Health to Beat Game

You are playing a game that has `n` levels numbered from `0` to `n - 1`. You are given a **0-indexed** integer array `damage` where `damage[i]` is the amount of health you will lose to complete the `ith` level.

You are also given an integer `armor`. You may use your armor ability **at most once** during the game on **any** level which will protect you from **at most** `armor` damage.

You must complete the levels in order and your health must be **greater than** `0` at all times to beat the game.

Return *the **minimum** health you need to start with to beat the game.*

**Example 1:**

```
Input: damage = [2,7,4,3], armor = 4
Output: 13
Explanation: One optimal way to beat the game starting at 13 health is:
On round 1, take 2 damage. You have 13 - 2 = 11 health.
On round 2, take 7 damage. You have 11 - 7 = 4 health.
On round 3, use your armor to protect you from 4 damage. You have 4 - 0 = 4 health.
On round 4, take 3 damage. You have 4 - 3 = 1 health.
Note that 13 is the minimum health you need to start with to beat the game.
```

**Example 2:**

```
Input: damage = [2,5,3,4], armor = 7
Output: 10
Explanation: One optimal way to beat the game starting at 10 health is:
On round 1, take 2 damage. You have 10 - 2 = 8 health.
On round 2, use your armor to protect you from 5 damage. You have 8 - 0 = 8 health.
On round 3, take 3 damage. You have 8 - 3 = 5 health.
On round 4, take 4 damage. You have 5 - 4 = 1 health.
Note that 10 is the minimum health you need to start with to beat the game.
```

**Example 3:**

```
Input: damage = [3,3,3], armor = 0
Output: 10
Explanation: One optimal way to beat the game starting at 10 health is:
On round 1, take 3 damage. You have 10 - 3 = 7 health.
On round 2, take 3 damage. You have 7 - 3 = 4 health.
On round 3, take 3 damage. You have 4 - 3 = 1 health.
Note that you did not use your armor ability.
```

**Constraints:**

- `n == damage.length`
- `1 <= n <= 105`
- `0 <= damage[i] <= 105`
- `0 <= armor <= 105`

**Solution**

```java
class Solution {
    public long minimumHealth(int[] damage, int armor) {
        
        //
        // 0 1 2 3 4 5 6 7 8
        // 2 7 4 3
        //       i
        // a = 4
        // s = 16 
        // m = 7
        // sum = s - min(m, a) + 1 
        // TC = O(N), SC = O(1)
        
        long sum = 0;
        int max = Integer.MIN_VALUE;
        
        for (int i = 0; i < damage.length; i++) {
            sum += damage[i];
            max = Math.max(max, damage[i]);
        }
        
        return sum - Math.min(max, armor) + 1;
    }
}
```



## LC#2193. Minimum Number of Moves to Make Palindrome

ou are given a string `s` consisting only of lowercase English letters.

In one **move**, you can select any two **adjacent** characters of `s` and swap them.

Return *the **minimum number of moves** needed to make* `s` *a palindrome*.

**Note** that the input will be generated such that `s` can always be converted to a palindrome.

 

**Example 1:**

```
Input: s = "aabb"
Output: 2
Explanation:
We can obtain two palindromes from s, "abba" and "baab". 
- We can obtain "abba" from s in 2 moves: "aabb" -> "abab" -> "abba".
- We can obtain "baab" from s in 2 moves: "aabb" -> "abab" -> "baab".
Thus, the minimum number of moves needed to make s a palindrome is 2.
```

**Example 2:**

```
Input: s = "letelt"
Output: 2
Explanation:
One of the palindromes we can obtain from s in 2 moves is "lettel".
One of the ways we can obtain it is "letelt" -> "letetl" -> "lettel".
Other palindromes such as "tleelt" can also be obtained in 2 moves.
It can be shown that it is not possible to obtain a palindrome in less than 2 moves.
```

**Constraints:**

- `1 <= s.length <= 2000`
- `s` consists only of lowercase English letters.
- `s` can be converted to a palindrome using a finite number of moves.

**Solution:**

```java
class Solution {
    // Which i don't at understand on 08/19/2022
    // TC = O(N^2), SC = O(N)
    public int minMovesToMakePalindrome(String s) {
        int res = 0;
        while (s.length() > 0) {
            int i = s.indexOf(s.charAt(s.length() - 1));
            if (i == s.length() - 1) {
                res += i / 2;
            } else {
                res += i;
                s = s.substring(0, i) + s.substring(i + 1);
            }
            s = s.substring(0, s.length() - 1);
        }
        return res;
    }
}
```



## LC#2262. Total Appeal of A String

The **appeal** of a string is the number of **distinct** characters found in the string.

- For example, the appeal of `"abbca"` is `3` because it has `3` distinct characters: `'a'`, `'b'`, and `'c'`.

Given a string `s`, return *the **total appeal of all of its \**substrings\**.***

A **substring** is a contiguous sequence of characters within a string.

**Example 1:**

```
Input: s = "abbca"
Output: 28
Explanation: The following are the substrings of "abbca":
- Substrings of length 1: "a", "b", "b", "c", "a" have an appeal of 1, 1, 1, 1, and 1 respectively. The sum is 5.
- Substrings of length 2: "ab", "bb", "bc", "ca" have an appeal of 2, 1, 2, and 2 respectively. The sum is 7.
- Substrings of length 3: "abb", "bbc", "bca" have an appeal of 2, 2, and 3 respectively. The sum is 7.
- Substrings of length 4: "abbc", "bbca" have an appeal of 3 and 3 respectively. The sum is 6.
- Substrings of length 5: "abbca" has an appeal of 3. The sum is 3.
The total sum is 5 + 7 + 7 + 6 + 3 = 28.
```

**Example 2:**

```
Input: s = "code"
Output: 20
Explanation: The following are the substrings of "code":
- Substrings of length 1: "c", "o", "d", "e" have an appeal of 1, 1, 1, and 1 respectively. The sum is 4.
- Substrings of length 2: "co", "od", "de" have an appeal of 2, 2, and 2 respectively. The sum is 6.
- Substrings of length 3: "cod", "ode" have an appeal of 3 and 3 respectively. The sum is 6.
- Substrings of length 4: "code" has an appeal of 4. The sum is 4.
The total sum is 4 + 6 + 6 + 4 = 20.
```

**Constraints:**

- `1 <= s.length <= 105`
- `s` consists of lowercase English letters.

**Solution:**

What is the appeal of all substrings that end at `i`-th position?

- It is the same as the appeal of all substrings that end at `i - 1` position, plus:
- number of substrings (ending at i-th position) that do not contain s[i] character.
  - to count those substrings, we just track the previous (`prev`) position of `s[i]` character.

![image](https://assets.leetcode.com/users/images/96c9f2ad-4378-461a-bcf0-3dd9596765be_1651377614.4588175.png)

```java
class Solution {
    public long appealSum(String s) {
        // 
        // 0  1  2  3  4  5  6  7  8  9 
        // a  b  b  c  a 
        //    i
        //    j
        //
        // 1  4  7  10 14
        //  
        // (total_) + pre_ap
        // TC = O(N^2), SC = O(N)
        // 
        // long dp = 1;
        // Set<Character> unique = new HashSet<>();
        // for (int i = 1; i < s.length(); i++) {
        //     long total = 0;
        //     for (int j = i; j >= 0; j--) {
        //         unique.add(s.charAt(j));
        //         total += unique.size();
        //     }
        //     dp += total;
        //     unique.clear();
        // }
        // return dp;
        
        long ans = 0;
        long cur = 0;
        long[] pre = new long[26];
        
        for (int i = 0; i < s.length(); i++) {
            cur += i + 1 - pre[s.charAt(i) - 'a'];
            pre[s.charAt(i) - 'a'] = i + 1;
            ans += cur;
        }
        return ans;
    }
}
```





# Day #22 (08/23/2022)

## LC#1152. Analyze User Website Visit Pattern

You are given two string arrays `username` and `website` and an integer array `timestamp`. All the given arrays are of the same length and the tuple `[username[i], website[i], timestamp[i]]` indicates that the user `username[i]` visited the website `website[i]` at time `timestamp[i]`.

A **pattern** is a list of three websites (not necessarily distinct).

- For example, `["home", "away", "love"]`, `["leetcode", "love", "leetcode"]`, and `["luffy", "luffy", "luffy"]` are all patterns.

The **score** of a **pattern** is the number of users that visited all the websites in the pattern in the same order they appeared in the pattern.

- For example, if the pattern is `["home", "away", "love"]`, the score is the number of users `x` such that `x` visited `"home"` then visited `"away"` and visited `"love"` after that.
- Similarly, if the pattern is `["leetcode", "love", "leetcode"]`, the score is the number of users `x` such that `x` visited `"leetcode"` then visited `"love"` and visited `"leetcode"` **one more time** after that.
- Also, if the pattern is `["luffy", "luffy", "luffy"]`, the score is the number of users `x` such that `x` visited `"luffy"` three different times at different timestamps.

Return *the **pattern** with the largest **score***. If there is more than one pattern with the same largest score, return the lexicographically smallest such pattern.

 

**Example 1:**

```
Input: username = ["joe","joe","joe","james","james","james","james","mary","mary","mary"], timestamp = [1,2,3,4,5,6,7,8,9,10], website = ["home","about","career","home","cart","maps","home","home","about","career"]
Output: ["home","about","career"]
Explanation: The tuples in this example are:
["joe","home",1],["joe","about",2],["joe","career",3],["james","home",4],["james","cart",5],["james","maps",6],["james","home",7],["mary","home",8],["mary","about",9], and ["mary","career",10].
The pattern ("home", "about", "career") has score 2 (joe and mary).
The pattern ("home", "cart", "maps") has score 1 (james).
The pattern ("home", "cart", "home") has score 1 (james).
The pattern ("home", "maps", "home") has score 1 (james).
The pattern ("cart", "maps", "home") has score 1 (james).
The pattern ("home", "home", "home") has score 0 (no user visited home 3 times).
```

**Example 2:**

```
Input: username = ["ua","ua","ua","ub","ub","ub"], timestamp = [1,2,3,4,5,6], website = ["a","b","a","a","b","c"]
Output: ["a","b","a"]
```

 

**Constraints:**

- `3 <= username.length <= 50`
- `1 <= username[i].length <= 10`
- `timestamp.length == username.length`
- `1 <= timestamp[i] <= 109`
- `website.length == username.length`
- `1 <= website[i].length <= 10`
- `username[i]` and `website[i]` consist of lowercase English letters.
- It is guaranteed that there is at least one user who visited at least three websites.
- All the tuples `[username[i], timestamp[i], website[i]]` are **unique**.

**Eddy's Solution**

```java
//         ROOT
//          |   
//        HOME(0)   
//       /      \
//   ABOUT(0)   CART(0) 
//      |        |
//  CAREER(2)   MAPS(0)
//               |
//              HOME(1)
//
// Assumptions:
// #1. Pattern ends with a user's last site visit
// #2. Timestamp is in ascending order.
// 
// TC = O(N) SC = O(|user| * |sites|)

class Solution {
    public List<String> mostVisitedPattern(String[] username, int[] timestamp, String[] website) {
        
        final int N = username.length;
        
        Map<String, List<String>> visits = new HashMap<>();
        
        for (int i = 0; i < N; i++) {
            List<String> personalHistory = visits.getOrDefault(username[i], new LinkedList<>());
            personalHistory.add(website[i]);
            visits.put(username[i], personalHistory);
        }
        
        Node root = new Node(-1);
        
        for (String name : visits.keySet()) {
            root.add(visits.get(name));
        }
        
        return findMaxPath(root);
    }
    
    private List<String> findMaxPath(Node root) {
        List<String> path = new ArrayList<>();
        List<String> result = new ArrayList<>();
        int[] maxScore = { Integer.MIN_VALUE };
        helper(root, path, maxScore, result);
        return result;
    }
    
    private void helper(Node node, List<String> path, int[] maxScore, List<String> result) {
        if (node.children.isEmpty()) {
            if (node.score > maxScore[0]) {
                // result = new ArrayList<>();
                result.clear();
                result.addAll(path);
                maxScore[0] = node.score;
            }
            return;
        }
        
        for (String child : node.children.keySet()) {
            path.add(child);
            helper(node.children.get(child), path, maxScore, result);
            path.remove(path.size() - 1);
        }
    }
}

class Node {
    
    public int score;
    
    public Map<String, Node> children; // site, child node
    
    public Node(int sc) {
        score = sc;
        children = new HashMap<>();
    }
    
    public void add(List<String> pattern) {
        String next = pattern.get(0);
        Node child = children.getOrDefault(next, new Node(0));
        if (pattern.size() == 1) {
            child.score++;
        } else {
            pattern.remove(0);
            child.add(pattern);
        }
        children.put(next, child);
    }
}
```



## LC#2102. Sequentially Ordinal Rank Tracker

A scenic location is represented by its `name` and attractiveness `score`, where `name` is a **unique** string among all locations and `score` is an integer. Locations can be ranked from the best to the worst. The **higher** the score, the better the location. If the scores of two locations are equal, then the location with the **lexicographically smaller** name is better.

You are building a system that tracks the ranking of locations with the system initially starting with no locations. It supports:

- **Adding** scenic locations, **one at a time**.
- Querying the i-th best location of all locations already added, where i is the number of times the system has been queried (including the current query).
  - For example, when the system is queried for the `4th` time, it returns the `4th` best location of all locations already added.

Note that the test data are generated so that **at any time**, the number of queries **does not exceed** the number of locations added to the system.

Implement the `SORTracker` class:

- `SORTracker()` Initializes the tracker system.
- `void add(string name, int score)` Adds a scenic location with `name` and `score` to the system.
- `string get()` Queries and returns the `ith` best location, where `i` is the number of times this method has been invoked (including this invocation).

 

**Example 1:**

```java
// Explanation
SORTracker tracker = new SORTracker(); // Initialize the tracker system.
tracker.add("bradford", 2); // Add location with name="bradford" and score=2 to the system.
tracker.add("branford", 3); // Add location with name="branford" and score=3 to the system.
tracker.get();              // The sorted locations, from best to worst, are: branford, bradford.
                            // Note that branford precedes bradford due to its higher score (3 > 2).
                            // This is the 1st time get() is called, so return the best location: "branford".
tracker.add("alps", 2);     // Add location with name="alps" and score=2 to the system.
tracker.get();              // Sorted locations: branford, alps, bradford.
                            // Note that alps precedes bradford even though they have the same score (2).
                            // This is because "alps" is lexicographically smaller than "bradford".
                            // Return the 2nd best location "alps", as it is the 2nd time get() is called.
tracker.add("orland", 2);   // Add location with name="orland" and score=2 to the system.
tracker.get();              // Sorted locations: branford, alps, bradford, orland.
                            // Return "bradford", as it is the 3rd time get() is called.
tracker.add("orlando", 3);  // Add location with name="orlando" and score=3 to the system.
tracker.get();              // Sorted locations: branford, orlando, alps, bradford, orland.
                            // Return "bradford".
tracker.add("alpine", 2);   // Add location with name="alpine" and score=2 to the system.
tracker.get();              // Sorted locations: branford, orlando, alpine, alps, bradford, orland.
                            // Return "bradford".
tracker.get();              // Sorted locations: branford, orlando, alpine, alps, bradford, orland.
                            // Return "orland".
```

 

**Constraints:**

- `name` consists of lowercase English letters, and is unique among all locations.
- `1 <= name.length <= 10`
- `1 <= score <= 105`
- At any time, the number of calls to `get` does not exceed the number of calls to `add`.
- At most `4 * 104` calls **in total** will be made to `add` and `get`.



**Solution:**

```java
// TC: all O(logN)
class SORTracker {

    private PriorityQueue<Node> minHeap;
    private PriorityQueue<Node> maxHeap;
    
    public SORTracker() {
        minHeap = new PriorityQueue<>();
        maxHeap = new PriorityQueue<>(Comparator.reverseOrder());
    }
    
    public void add(String name, int score) {
        Node node = new Node(name, score);
        minHeap.offer(node);
        maxHeap.offer(minHeap.poll());
    }
    
    public String get() {
        Node node = maxHeap.poll();
        minHeap.offer(node);
        return node.name;
    }
}

class Node implements Comparable<Node> {
    public String name;
    public int score;
    
    public Node(String n, int s) {
        name = n;
        score = s;
    }
    
    @Override
    public int compareTo(Node other) {
        if (this.score == other.score) {
            return other.name.compareTo(this.name);
        }
        return this.score - other.score;
    }
}
```



# Day #23 (08/26/2022)

## LC#2130. Maximum Twin Sum of a Linked List

In a linked list of size `n`, where `n` is **even**, the `ith` node (**0-indexed**) of the linked list is known as the **twin** of the `(n-1-i)th` node, if `0 <= i <= (n / 2) - 1`.

- For example, if `n = 4`, then node `0` is the twin of node `3`, and node `1` is the twin of node `2`. These are the only nodes with twins for `n = 4`.

The **twin sum** is defined as the sum of a node and its twin.

Given the `head` of a linked list with even length, return *the **maximum twin sum** of the linked list*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/12/03/eg1drawio.png)

```
Input: head = [5,4,2,1]
Output: 6
Explanation:
Nodes 0 and 1 are the twins of nodes 3 and 2, respectively. All have twin sum = 6.
There are no other nodes with twins in the linked list.
Thus, the maximum twin sum of the linked list is 6. 
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/12/03/eg2drawio.png)

```
Input: head = [4,2,2,3]
Output: 7
Explanation:
The nodes with twins present in this linked list are:
- Node 0 is the twin of node 3 having a twin sum of 4 + 3 = 7.
- Node 1 is the twin of node 2 having a twin sum of 2 + 2 = 4.
Thus, the maximum twin sum of the linked list is max(7, 4) = 7. 
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2021/12/03/eg3drawio.png)

```
Input: head = [1,100000]
Output: 100001
Explanation:
There is only one node with a twin in the linked list having twin sum of 1 + 100000 = 100001.
```

**Constraints:**

- The number of nodes in the list is an **even** integer in the range `[2, 105]`.
- `1 <= Node.val <= 105`

**Solution**

```java
// Solution #1. Brute Force: TC = O(N^2), SC = O(1)
// Solution #2. Copy linked list into indexed list: TC = O(N), SC = O(N)
// Solution #3. Break linked list 2 halves, reverse the second half, then iterate: TC = O(N), SC = O(1)

class Solution {
    
    public int pairSum(ListNode head) {
        ListNode firstHalf = head;
        ListNode mid = findMid(head);
        ListNode secondHalf = mid.next;
        mid.next = null;
        
        secondHalf = reverse(secondHalf);
        
        int max = Integer.MIN_VALUE;
        ListNode firstCur = firstHalf;
        ListNode secondCur = secondHalf;
        while (firstCur != null) {
            max = Math.max(firstCur.val + secondCur.val, max);
            firstCur = firstCur.next;
            secondCur = secondCur.next;
        }
        return max;
    }
    
    private ListNode findMid(ListNode head) {
        
        if (head == null) {
            return head;
        }
        ListNode fast = head;
        ListNode slow = head;
        while (fast.next != null && fast.next.next != null) {
            fast = fast.next.next;
            slow = slow.next;
        }
        return slow;
    }
    
    // Use iterative
    private ListNode reverse(ListNode head) {
        
        if (head == null || head.next == null) {
            return head;
        }
        
        ListNode pre = null;
        ListNode cur = head;
        ListNode pos = head.next;
        
        while (pos != null) {
            cur.next = pre;
            pre = cur;
            cur = pos;
            pos = pos.next;
        }
        cur.next = pre;
        return cur;
    }   
}
```



## LC#545. Boundary of Binary Tree

The **boundary** of a binary tree is the concatenation of the **root**, the **left boundary**, the **leaves** ordered from left-to-right, and the **reverse order** of the **right boundary**.

The **left boundary** is the set of nodes defined by the following:

- The root node's left child is in the left boundary. If the root does not have a left child, then the left boundary is **empty**.
- If a node in the left boundary and has a left child, then the left child is in the left boundary.
- If a node is in the left boundary, has **no** left child, but has a right child, then the right child is in the left boundary.
- The leftmost leaf is **not** in the left boundary.

The **right boundary** is similar to the **left boundary**, except it is the right side of the root's right subtree. Again, the leaf is **not** part of the **right boundary**, and the **right boundary** is empty if the root does not have a right child.

The **leaves** are nodes that do not have any children. For this problem, the root is **not** a leaf.

Given the `root` of a binary tree, return *the values of its **boundary***.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/11/boundary1.jpg)

```
Input: root = [1,null,2,3,4]
Output: [1,3,4,2]
Explanation:
- The left boundary is empty because the root does not have a left child.
- The right boundary follows the path starting from the root's right child 2 -> 4.
  4 is a leaf, so the right boundary is [2].
- The leaves from left to right are [3,4].
Concatenating everything results in [1] + [] + [3,4] + [2] = [1,3,4,2].
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/11/11/boundary2.jpg)

```
Input: root = [1,2,3,4,5,6,null,null,null,7,8,9,10]
Output: [1,2,4,7,8,9,10,6,3]
Explanation:
- The left boundary follows the path starting from the root's left child 2 -> 4.
  4 is a leaf, so the left boundary is [2].
- The right boundary follows the path starting from the root's right child 3 -> 6 -> 10.
  10 is a leaf, so the right boundary is [3,6], and in reverse order is [6,3].
- The leaves from left to right are [4,7,8,9,10].
Concatenating everything results in [1] + [2] + [4,7,8,9,10] + [6,3] = [1,2,4,7,8,9,10,6,3].
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[1, 104]`.
- `-1000 <= Node.val <= 1000`



**Solution (Eddy's)**

```java
class Solution {
    private int height;
    
    public List<Integer> boundaryOfBinaryTree(TreeNode root) {
        height = height(root);
        
        List<Integer> result = new ArrayList<>();
        addLeft(root, result, 0);

        List<Integer> bottom = new ArrayList<>();
        addBottom(root, bottom, 0);
        bottom.remove(0);
        result.addAll(bottom);
        
        List<Integer> right = new ArrayList<>();
        addRight(root, right, 0);
        right.remove(0);
        right.remove(right.size() - 1);
        Collections.reverse(right);
        result.addAll(right);
        
        return result;
    }
    
    
    private int height(TreeNode root) {
        if (root == null) {
            return 0;
        }
        int left = height(root.left);
        int right = height(root.right);
        return Math.max(left, right) + 1;
    }
    
    public void addLeft(TreeNode root, List<Integer> list, int curHeight) {
		if (root == null) {
			return;
		}

		if (list.size() == curHeight) {
			list.add(root.val);
		}
		addLeft(root.left, list, curHeight + 1);
		addLeft(root.right, list, curHeight + 1);
	}

	public void addBottom(TreeNode root, List<Integer> list, int curHeight) {
		if (root == null) {
			return;
		}

		addBottom(root.left, list, curHeight + 1);
		if (curHeight == this.height - 1) {
			list.add(root.val);
		}
		addBottom(root.right, list, curHeight + 1);

	}

	public void addRight(TreeNode root, List<Integer> list, int curHeight) {
		if (root == null) {
			return;
		}

		if (list.size() == curHeight) {
			list.add(root.val);
		}
		addRight(root.right, list, curHeight + 1);
		addRight(root.left, list, curHeight + 1);
	}
}
```



## LC#665. Non-decreasing Array

Given an array `nums` with `n` integers, your task is to check if it could become non-decreasing by modifying **at most one element**.

We define an array is non-decreasing if `nums[i] <= nums[i + 1]` holds for every `i` (**0-based**) such that (`0 <= i <= n - 2`).

 

**Example 1:**

```
Input: nums = [4,2,3]
Output: true
Explanation: You could modify the first 4 to 1 to get a non-decreasing array.
```

**Example 2:**

```
Input: nums = [4,2,1]
Output: false
Explanation: You cannot get a non-decreasing array by modifying at most one element.
```

 

**Constraints:**

- `n == nums.length`
- `1 <= n <= 104`
- `-105 <= nums[i] <= 105`

**Solution**

```java
class Solution {
    // 不成体系的problem. See Eddy's submission on LC website
    public boolean checkPossibility(int[] nums) {
        int badIdx = -1;
        
        for (int i = 0; i < nums.length - 1; i++) {
            if (nums[i] > nums[i + 1]) {
                if (badIdx != -1) {
                    return false;
                } 
                badIdx = i;
            }
        }
        
        if (badIdx <= 0 || badIdx == nums.length - 2) {
            return true;
        }
        
        return nums[badIdx - 1] <= nums[badIdx + 1] || nums[badIdx] <= nums[badIdx + 2];
    }
}
```





# Day #24 (10/03/2022)

## LC#1444. Number of Ways of Cutting a Pizza

Given a rectangular pizza represented as a `rows x cols` matrix containing the following characters: `'A'` (an apple) and `'.'` (empty cell) and given the integer `k`. You have to cut the pizza into `k` pieces using `k-1` cuts. 

For each cut you choose the direction: vertical or horizontal, then you choose a cut position at the cell boundary and cut the pizza into two pieces. If you cut the pizza vertically, give the left part of the pizza to a person. If you cut the pizza horizontally, give the upper part of the pizza to a person. Give the last piece of pizza to the last person.

*Return the number of ways of cutting the pizza such that each piece contains **at least** one apple.* Since the answer can be a huge number, return this modulo 10^9 + 7.

 

**Example 1:**

**![img](https://assets.leetcode.com/uploads/2020/04/23/ways_to_cut_apple_1.png)**

```
Input: pizza = ["A..","AAA","..."], k = 3
Output: 3 
Explanation: The figure above shows the three ways to cut the pizza. Note that pieces must contain at least one apple.
```

**Example 2:**

```
Input: pizza = ["A..","AA.","..."], k = 3
Output: 1
```

**Example 3:**

```
Input: pizza = ["A..","A..","..."], k = 1
Output: 1
```

**Constraints:**

- `1 <= rows, cols <= 50`
- `rows == pizza.length`
- `cols == pizza[i].length`
- `1 <= k <= 10`
- `pizza` consists of characters `'A'` and `'.'` only.

**Solution:**

```java
class Solution {
    public int ways(String[] pizza, int k) {
        final int N = pizza.length; 
        final int M = pizza[0].length();
        
        Integer[][][] dp = new Integer[k][N][M];
        int[][] preSum = new int[N + 1][M + 1];
        
        for (int i = N - 1; i >= 0; i--) {
            for (int j = M - 1; j >= 0; j--) {
                preSum[i][j] = preSum[i][j + 1] + preSum[i + 1][j] - preSum[i + 1][j + 1] + (pizza[i].charAt(j) == 'A'? 1 : 0);
            }
        }
        
        return dfs(N, M, k - 1, 0, 0, dp, preSum);
    }
    
    private int dfs(int N, int M, int k, int r, int c, Integer[][][] dp, int[][] preSum) {
        if (preSum[r][c] == 0) {
            return 0;
        }
        
        if (k == 0) {
            return 1;
        }
        
        if (preSum[r][c] < k) {
            return 0;
        }
        
        if (dp[k][r][c] != null) {
            return dp[k][r][c];
        }
        
        int ans = 0;
        
        for (int i = r + 1; i < N; i++) {
            if (preSum[r][c] - preSum[i][c] > 0) {
                ans += dfs(N, M, k - 1, i, c, dp, preSum);
                ans %= 1_000_000_007;
            }
        }
        
        for (int j = c + 1; j < M; j++) {
            if (preSum[r][c] - preSum[r][j] > 0) {
                ans += dfs(N, M, k - 1, r, j, dp, preSum);
                ans %= 1_000_000_007;
            }
        }
        
        return dp[k][r][c] = ans;
    }
}
```





## LC#322. Coin Change

You are given an integer array `coins` representing coins of different denominations and an integer `amount` representing a total amount of money.

Return *the fewest number of coins that you need to make up that amount*. If that amount of money cannot be made up by any combination of the coins, return `-1`.

You may assume that you have an infinite number of each kind of coin.

 

**Example 1:**

```
Input: coins = [1,2,5], amount = 11
Output: 3
Explanation: 11 = 5 + 5 + 1
```

**Example 2:**

```
Input: coins = [2], amount = 3
Output: -1
```

**Example 3:**

```
Input: coins = [1], amount = 0
Output: 0
```

 

**Constraints:**

- `1 <= coins.length <= 12`
- `1 <= coins[i] <= 231 - 1`
- `0 <= amount <= 104`



**Solution:**

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        for (int i = 1; i <= amount; i++) {
            dp[i] = amount + 1;
            for (int j = 0; j < coins.length; j++) {
                if (coins[j] <= i) {
                    dp[i] = Math.min(dp[i], dp[i - coins[j]] + 1);
                }
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
    }
}
```



## TikTok OA Bit-OP multi-choice

```java
public static void main(String[] args) {
	boolean result = true;
	for (int a = 0; a < 1000; a++) {
		for (int b = 0; b < 1000; b++) {
			// int c = (~a & b) + (a & ~b) + (a & b);     // NO
			// int c = (a & b) + (a | b);                 // YES
			// int c = (a^b) + (a & b) * 2;               // YES
			int c = (~a & b) + (a & ~b) + 2 * (a & b);    // YES

			if (a + b != c) {
				System.out.println("a: " + a + ", b: " + b + ", c: " + c);
				result = false;
				break;	
			}
		}

		if (result == false) {
			break;
		}
	}
	System.out.println(result);
}
```



## TikTok Coding #3

```java
private static boolean transformArray(String a, String b) {
    String[] arrA = a.split(" ");
    Arrays.sort(arrA);

    String[] arrB = b.split(" ");
    Arrays.sort(arrB);

    for (int i = 0; i < arrA.length; i++) {
        int a = Integer.parseInt(arrA[i]);
        int b = Integer.parseInt(arrB[i]);

        if (a != b && a + 1 != b) {
            return false;
        }
    }

    return true;
}
```



## TikTok OA Push Stack Multi-Choice

选择第二题是 stack. stack push 的顺序是1 2 3 4 ... n, stack pop的顺序是p1, p2, p3, ... pn. 问如果p2 = 3, p3有几种可能。

分享题的人觉得是 N - 1
