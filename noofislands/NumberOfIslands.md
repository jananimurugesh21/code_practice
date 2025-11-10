# BFS (Breadth-First Search) Explanation - Number of Islands

## What is BFS?

**Breadth-First Search (BFS)** is a graph/grid traversal algorithm that explores nodes **level by level**. It visits all neighbors at the current level before moving to the next level.

Think of it like ripples in water - when you drop a stone, the ripples spread outward in circles. BFS works the same way!

---

## The BFS Code

```java
private void bfs(char[][] grid, boolean[][] visited, int r, int c, int[] dr, int[] dc) {
    Deque<int[]> queue = new ArrayDeque<>();
    queue.offer(new int[]{r, c});
    visited[r][c] = true;

    while (!queue.isEmpty()) {
        int[] cell = queue.poll();
        int row = cell[0], col = cell[1];

        for (int i = 0; i < 4; i++) {
            int nr = row + dr[i];
            int nc = col + dc[i];

            if (nr >= 0 && nr < grid.length && nc >= 0 && nc < grid[0].length
                    && grid[nr][nc] == '1' && !visited[nr][nc]) {
                queue.offer(new int[]{nr, nc});
                visited[nr][nc] = true;
            }
        }
    }
}
```

---

## Step-by-Step Example

### Given Grid (Simple 3x3):
```
     Col: 0   1   2
Row 0:   1   1   0
Row 1:   1   0   0
Row 2:   0   0   1
```

Let's say we found our **first unvisited '1'** at position **(0, 0)**.

---

### **Initial Setup**

```java
Deque<int[]> queue = new ArrayDeque<>();
queue.offer(new int[]{0, 0});  // Add starting position
visited[0][0] = true;           // Mark as visited
```

**Queue state:** `[(0,0)]`

**Visited grid:**
```
     0   1   2
0:  [V]  .   .
1:   .   .   .
2:   .   .   .
```

---

## Iteration by Iteration

### **Iteration 1**

#### Step 1: Dequeue
```java
int[] cell = queue.poll();  // Remove (0,0) from queue
int row = 0, col = 0;
```
**Queue:** `[]` (empty now)

#### Step 2: Check all 4 neighbors

**Direction arrays:**
- `dr = {-1, 1, 0, 0}` (up, down, left, right)
- `dc = {0, 0, -1, 1}`

**Loop through i = 0, 1, 2, 3:**

**i = 0 (UP):**
```java
nr = 0 + (-1) = -1
nc = 0 + 0 = 0
```
❌ Out of bounds (nr < 0) - skip

**i = 1 (DOWN):**
```java
nr = 0 + 1 = 1
nc = 0 + 0 = 0
```
✅ Valid! `grid[1][0] = '1'` and not visited
```java
queue.offer(new int[]{1, 0});
visited[1][0] = true;
```
**Queue:** `[(1,0)]`

**i = 2 (LEFT):**
```java
nr = 0 + 0 = 0
nc = 0 + (-1) = -1
```
❌ Out of bounds (nc < 0) - skip

**i = 3 (RIGHT):**
```java
nr = 0 + 0 = 0
nc = 0 + 1 = 1
```
✅ Valid! `grid[0][1] = '1'` and not visited
```java
queue.offer(new int[]{0, 1});
visited[0][1] = true;
```
**Queue:** `[(1,0), (0,1)]`

**Visited after Iteration 1:**
```
     0   1   2
0:  [V] [V]  .
1:  [V]  .   .
2:   .   .   .
```

---

### **Iteration 2**

#### Step 1: Dequeue
```java
int[] cell = queue.poll();  // Remove (1,0)
int row = 1, col = 0;
```
**Queue:** `[(0,1)]`

#### Step 2: Check neighbors of (1,0)

**i = 0 (UP):** (0,0) - Already visited ❌
**i = 1 (DOWN):** (2,0) - It's '0' (water) ❌
**i = 2 (LEFT):** Out of bounds ❌
**i = 3 (RIGHT):** (1,1) - It's '0' (water) ❌

No new cells added!
**Queue:** `[(0,1)]`

**Visited after Iteration 2:**
```
     0   1   2
0:  [V] [V]  .
1:  [V]  .   .
2:   .   .   .
```
(No changes)

---

### **Iteration 3**

#### Dequeue (0,1)
Check neighbors:
- **UP:** (-1,1) - Out of bounds ❌
- **DOWN:** (1,1) - It's '0' (water) ❌
- **LEFT:** (0,0) - Already visited ❌
- **RIGHT:** (0,2) - It's '0' (water) ❌

No new cells added!
**Queue:** `[]` (empty!)

**Final Visited:**
```
     0   1   2
0:  [V] [V]  .
1:  [V]  .   .
2:   .   .   .
```

Queue is empty → BFS completes!

**Result:** We explored the entire island starting from (0,0). This island has 3 cells: (0,0), (0,1), and (1,0).

Note: Cell (2,2) is a separate island that would be found in the main loop later!

---

## Complete Example with Main Loop

Now let's see how the **main loop** (`numIslands` method) uses BFS to find ALL islands:

### **Starting Grid:**
```
     0   1   2
0:   1   1   0
1:   1   0   0
2:   0   0   1
```

### **Main Loop Code:**
```java
public int numIslands(char[][] grid) {
    int rows = 3, cols = 3;
    boolean[][] visited = new boolean[3][3];
    int count = 0;

    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (grid[r][c] == '1' && !visited[r][c]) {
                count++;
                bfs(grid, visited, r, c, dr, dc);
            }
        }
    }
    return count;
}
```

---

### **Main Loop Execution:**

#### **Loop Position: (0,0)**
```
Checking: grid[0][0] = '1' and visited[0][0] = false
```
✅ **Found an unvisited island!**
- `count++` → **count = 1**
- Call `bfs(grid, visited, 0, 0, dr, dc)`
- BFS explores and marks: (0,0), (0,1), (1,0) as visited

**Visited after BFS:**
```
     0   1   2
0:  [V] [V]  .
1:  [V]  .   .
2:   .   .   .
```

---

#### **Loop Position: (0,1)**
```
Checking: grid[0][1] = '1' and visited[0][1] = true
```
❌ Already visited → Skip (no count increment)

---

#### **Loop Position: (0,2)**
```
Checking: grid[0][2] = '0'
```
❌ It's water → Skip

---

#### **Loop Position: (1,0)**
```
Checking: grid[1][0] = '1' and visited[1][0] = true
```
❌ Already visited → Skip

---

#### **Loop Position: (1,1)**
```
Checking: grid[1][1] = '0'
```
❌ It's water → Skip

---

#### **Loop Position: (1,2)**
```
Checking: grid[1][2] = '0'
```
❌ It's water → Skip

---

#### **Loop Position: (2,0)**
```
Checking: grid[2][0] = '0'
```
❌ It's water → Skip

---

#### **Loop Position: (2,1)**
```
Checking: grid[2][1] = '0'
```
❌ It's water → Skip

---

#### **Loop Position: (2,2)**
```
Checking: grid[2][2] = '1' and visited[2][2] = false
```
✅ **Found another unvisited island!**
- `count++` → **count = 2**
- Call `bfs(grid, visited, 2, 2, dr, dc)`

**BFS from (2,2):**

**Initial:** Queue = [(2,2)], mark (2,2) visited

**Iteration 1:** Process (2,2)
- Check UP: (1,2) is '0' → skip
- Check DOWN: (3,2) out of bounds → skip
- Check LEFT: (2,1) is '0' → skip
- Check RIGHT: (2,3) out of bounds → skip
- Queue = [] (empty!)

**Visited after second BFS:**
```
     0   1   2
0:  [V] [V]  .
1:  [V]  .   .
2:   .   .  [V]
```

This island has only 1 cell: (2,2)

---

### **Final Result:**

Main loop completes after checking all 9 cells (3×3 grid).

**Answer: `count = 2`** (Two islands found!)

**Island 1:** Cells (0,0), (0,1), (1,0) - 3 cells
**Island 2:** Cell (2,2) - 1 cell

---
