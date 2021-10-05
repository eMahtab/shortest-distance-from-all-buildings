# Shortest distance from all buildings
## https://leetcode.com/problems/shortest-distance-from-all-buildings

You are given an m x n grid grid of values 0, 1, or 2, where:

each 0 marks an empty land that you can pass by freely,
each 1 marks a building that you cannot pass through, and
each 2 marks an obstacle that you cannot pass through.

You want to build a house on an empty land that reaches all buildings in the shortest total travel distance. You can only move up, down, left, and right.

Return the shortest travel distance for such a house. If it is not possible to build such a house according to the above rules, return -1.

The total travel distance is the sum of the distances between the houses of the friends and the meeting point.

The distance is calculated using Manhattan Distance, where distance(p1, p2) = |p2.x - p1.x| + |p2.y - p1.y|.


!["Shortest Distance from all buildings"](example.JPG?raw=true)
```
Input: grid = [[1,0,2,0,1],[0,0,0,0,0],[0,0,1,0,0]]
Output: 7
Explanation: Given three buildings at (0,0), (0,4), (2,2), and an obstacle at (0,2).
The point (1,2) is an ideal empty land to build a house, as the total travel distance of 3+3+1=7 is minimal. 
So return 7.
```

## Naive approach : Works but runs out of time 
Store the locations of all the buildings, and do BFS from the `zero` cells to each of the building to get the total distance.
And return the global minimum distance. 
This approach works but the issue is, if we have a very large input matrix with lots of`zeroes` then its going to take a lot of time, 
since we are doing **BFS from each `zero` cell to each of the building**.


# Implementation : Time Limit exceeded
```java
class Solution {
    public int shortestDistance(int[][] grid) {
        if(grid == null || grid.length == 0)
            return 0;
        
        int rows = grid.length;
        int cols = grid[0].length;
        List<int[]> buildings = new ArrayList<>();
        for(int i = 0; i < rows; i++) {
            for(int j = 0; j < cols; j++) {
                if(grid[i][j] == 1)
                    buildings.add(new int[]{i, j});
            }
        }
        int minDistance = Integer.MAX_VALUE;
        boolean solutionPossible = false;
        
        for(int i = 0; i < rows ; i++) {
            for(int j = 0; j < cols; j++) {
                if(grid[i][j] == 0) {
                    int distance = 0;
                    boolean canReach = true;
                    for(int[] building: buildings) {
                        int d = getDistance(grid, i, j, building[0], building[1]);
                        if(d == -1) {
                            canReach = false;
                            break;
                        }
                        distance += d;
                    }
                    if(canReach) {
                        minDistance = Math.min(minDistance, distance);
                        solutionPossible = true;
                    }
                }
            }
        }
        
       return solutionPossible ? minDistance : -1; 
    }
    
    private int getDistance(int[][] grid, int sx, int sy, int dx, int dy) {
        Set<String> visited = new HashSet<>();
        Queue<int[]> q = new ArrayDeque<>();
        q.add(new int[]{sx, sy});
        visited.add(sx + "," + sy);
        int distance = 1;
        int[][] moves = {{0, 1} , {0, -1} , {-1, 0} , {1, 0}};
        while(!q.isEmpty()) {
            int size = q.size();
            for(int i = 0; i < size; i++) {
                int[] pos = q.remove();
                for(int[] move : moves) {
                    int x = pos[0] + move[0];
                    int y = pos[1] + move[1];
                    if( x >= 0 &&  x < grid.length && y >= 0 && y < grid[0].length) {
                        if(x == dx && y == dy)
                            return distance;
                        if(!visited.contains(x+","+y) && grid[x][y] == 0) {
                            q.add(new int[]{x , y});
                            visited.add(x + "," + y);
                        }
                            
                    }
                }
            }
            distance++;
        }
        return -1;
    }
}
```
