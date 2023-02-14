---
layout: default
title: 게임 맵 최단거리(BFS 문제)
parent: 깊이/너비 우선 탐색(DFS/BFS) 알고리즘
grand_parent: Algorithm
permalink: /algorithm/dfs_bfs/bfs-algo-1/
nav_order: 98
---

## 문제설명

 ROR 게임은 두 팀으로 나누어서 진행하며, 상대 팀 진영을 먼저 파괴하면 이기는 게임입니다. 따라서, 각 팀은 상대 팀 진영에 최대한 빨리 도착하는 것이 유리합니다. 지금부터 당신은 한 팀의 팀원이 되어 게임을 진행하려고 합니다. 

 다음은 5 x 5 크기의 맵에, 당신의 캐릭터가 (행: 1, 열: 1) 위치에 있고, 상대 팀 진영은 (행: 5, 열: 5) 위치에 있는 경우의 예시입니다.

 * bfs_algo_1_01.png
 위 그림에서 검은색 부분은 벽으로 막혀있어 갈 수 없는 길이며, 흰색 부분은 갈 수 있는 길입니다. 캐릭터가 움직일 때는 동, 서, 남, 북 방향으로 한 칸씩 이동하며, 게임 맵을 벗어난 길은 갈 수 없습니다.

 아래 예시는 캐릭터가 상대 팀 진영으로 가는 두 가지 방법을 나타내고 있습니다. 첫 번째 방법은 11개의 칸을 지나서 상대 팀 진영에 도착했습니다.
 bfs_algo_1_02.png 

 두 번째 방법은 15개의 칸을 지나서 상대팀 진영에 도착했습니다.
 bfs_algo_1_03.png 

 위 예시에서는 첫 번째 방법보다 더 빠르게 상대팀 진영에 도착하는 방법은 없으므로, 이 방법이 상대 팀 진영으로 가는 가장 빠른 방법입니다. 만약, 상대 팀이 자신의 팀 진영 주위에 벽을 세워두었다면 상대 팀 진영에 도착하지 못할 수도 있습니다. 예를 들어, 다음과 같은 경우에 당신의 캐릭터는 상대 팀 진영에 도착할 수 없습니다.
 bfs_algo_1_04.png
 
 게임 맵의 상태 maps가 매개변수로 주어질 때, 캐릭터가 상대 팀 진영에 도착하기 위해서 지나가야 하는 칸의 개수의 최솟값을 return 하도록 solution 함수를 완성해주세요. 단, 상대 팀 진영에 도착할 수 없을 때는 -1을 return 해주세요.


## 제한사항
 - maps는 n x m 크기의 게임 맵의 상태가 들어있는 2차원 배열로, n과 m은 각각 1 이상 100 이하의 자연수입니다.
 - n과 m은 서로 같을 수도, 다를 수도 있지만, n과 m이 모두 1인 경우는 입력으로 주어지지 않습니다.
 - maps는 0과 1로만 이루어져 있으며, 0은 벽이 있는 자리, 1은 벽이 없는 자리를 나타냅니다.
 - 처음에 캐릭터는 게임 맵의 좌측 상단인 (1, 1) 위치에 있으며, 상대방 진영은 게임 맵의 우측 하단인 (n, m) 위치에 있습니다.


## 입출력 예
```text
maps															answer

[[1,0,1,1,1],[1,0,1,0,1],[1,0,1,1,1],[1,1,1,0,1],[0,0,0,0,1]]	11

[[1,0,1,1,1],[1,0,1,0,1],[1,0,1,1,1],[1,1,1,0,0],[0,0,0,0,1]]	-1
```
### 입출력 예 설명
입출력 예 #1
주어진 데이터는 다음과 같습니다.
bfs_algo_1_05.png
캐릭터가 적 팀의 진영까지 이동하는 가장 빠른 길은 다음 그림과 같습니다.
bfs_algo_1_06.png
따라서 총 11칸을 캐릭터가 지나갔으므로 11을 return 하면 됩니다.

입출력 예 #2 
문제의 예시와 같으며, 상대 팀 진영에 도달할 방법이 없습니다. 따라서 -1을 return 합니다.

## 입출력 예
 - 출처: 프로그래머스 - https://school.programmers.co.kr/learn/courses/30/lessons/1844


## 문제풀이

### 본인답안
```java
import java.util.*;

public class 게임_맵_최단거리 {
	
    public static void main(String[] args) {
    	// numbers			target	return
    	// [1, 1, 1, 1, 1]	3		5
    	// [4, 1, 2, 1]		4		2
    	게임_맵_최단거리 obj = new 게임_맵_최단거리();
		int[][] maps = {{1,0,1,1,1},{1,0,1,0,1},{1,0,1,1,1},{1,1,1,0,1},{0,0,0,0,1}};
		System.out.println( obj.solution(maps));
		int[][] maps2 = {{1,0,1,1,1},{1,0,1,0,1},{1,0,1,1,1},{1,1,1,0,0},{0,0,0,0,1}};
		System.out.println( obj.solution(maps2));
	}
    
    private boolean[][] visitCheckArr;
    private int[][] currentMap;

    private int sourceX;
    private int sourceY;

    /* 오답에 사용한 전역변수 */
    private int leastVal = -1;
    private int[][] originalMaps;
    
    /* 최종 답변 */
    public int solution(int[][] maps) {
        int answer = 0;

        //XY의 최대 좌표
        sourceX = maps[0].length;
        sourceY = maps.length;

        //방문여부 저장하는 배열
        visitCheckArr = new boolean[sourceY][sourceX];
        currentMap = new int[sourceY][sourceX];
        for(int i = 0; i < sourceY; i++){
            for(int j = 0; j < sourceX; j++){
                visitCheckArr[i][j] = false;
                currentMap[i][j] = maps[i][j];
            }
        }
        
        if(bfs(0, 0)){
            answer =  currentMap[sourceY-1][sourceX-1];
        }else{
            answer = -1;
        }

        return answer;
    }

    public boolean bfs(int x, int y){
        Queue<int[]> queue = new LinkedList<int[]>();
        int[] startNode = {x,y,1};
        queue.add(startNode);
        while(!queue.isEmpty()){
            int[] currentNode = queue.poll();

            int currentX = currentNode[0];
            int currentY = currentNode[1];
            int currentDepth = currentNode[2];

            visitCheckArr[currentY][currentX] = true;
            currentMap[currentY][currentX] = currentDepth;

            int[] upXY = {currentX, currentY-1,currentDepth+1};
            int[] downXY = {currentX, currentY+1, currentDepth+1};
            int[] rightXY = {currentX+1, currentY,currentDepth+1};
            int[] leftXY = {currentX-1, currentY, currentDepth+1};

            int[][] nextNodeArr = {upXY, downXY, rightXY, leftXY};
            for(int[] nextNode : nextNodeArr){
                if(nextNode[0] >= sourceX || nextNode[0] < 0 
                  || nextNode[1] >= sourceY || nextNode[1] < 0){
                    continue;
                }else if(visitCheckArr[nextNode[1]][nextNode[0]] ||
                         currentMap[nextNode[1]][nextNode[0]] == 0){ 
                    continue;
                }else if(currentMap[nextNode[1]][nextNode[0]] > nextNode[2] || currentMap[nextNode[1]][nextNode[0]] == 1 ){
                    currentMap[nextNode[1]][nextNode[0]] = nextNode[2];
                    queue.add(nextNode);
                }
            }
        }
        return visitCheckArr[sourceY-1][sourceX-1];
    }
```

### dfs로 풀어서 답은 맞았지만 효율성에서 틀린 답안
```java
    public int dfs(int[][] maps, int x, int y, int moveCount){
        if(y == maps.length-1 && x == maps[y].length-1){
            return moveCount;
        }else if(y >= maps.length || y<0 || x >= maps[y].length || x<0){
            return -1;
        }else if(maps[y][x]==0){
            return -1;
        }else if(leastVal != -1 && moveCount >= leastVal){
            return -1;
        }else{
            if(originalMaps[y][x] != 1 && originalMaps[y][x] < moveCount ){
                System.out.println("position x: " + x + ", y: " + y + " and moveCount is " + moveCount);
                System.out.println("originalMaps[y][x] - " + originalMaps[y][x]);
                System.out.println("It will return -1");
                return -1;
            }else{
                System.out.println("position x: " + x + ", y: " + y + " and moveCount is " + moveCount);
                System.out.println("originalMaps[y][x] - " + originalMaps[y][x]);
                originalMaps[y][x] = moveCount;
                System.out.println("after originalMaps[y][x] - " + originalMaps[y][x]);
            }
            
            int[][] newMaps1 = new int[maps.length][maps[0].length];
            int[][] newMaps2 = new int[maps.length][maps[0].length];
            int[][] newMaps3 = new int[maps.length][maps[0].length];
            int[][] newMaps4 = new int[maps.length][maps[0].length];
            
            for(int i = 0; i < maps.length; i++){
                for(int j = 0; j < maps[0].length; j++){
                    newMaps1[i][j] = maps[i][j];
                    newMaps2[i][j] = maps[i][j];
                    newMaps3[i][j] = maps[i][j];
                    newMaps4[i][j] = maps[i][j];
                }
            }
            
            newMaps1[y][x]=0;
            newMaps2[y][x]=0;
            newMaps3[y][x]=0;
            newMaps4[y][x]=0;
            int right = dfs(newMaps1,x+1,y,moveCount+1);
            int left = dfs(newMaps2,x-1,y,moveCount+1);
            int down = dfs(newMaps3,x,y+1,moveCount+1);
            int up = dfs(newMaps4,x,y-1,moveCount+1);
            
            int[] a = {right,left,down,up};
            for(int i = 0; i < a.length; i++){
                if(a[i] != -1 && (a[i] < leastVal || leastVal == -1)){ 
                    leastVal = a[i]; 
                }
            }
                
            return leastVal;
        }
    }
```
### 점수가 높았던 답안

```java
    public static int answer(int[][] maps) {
        int X = maps[0].length;
        int Y = maps.length;
        boolean[][] visited = new boolean[Y][X];
        Queue<Point> q = new LinkedList<Point>();
        int x = 0;
        int y = 0;
        int size = 0;
        int cnt = 0;
        Point p = new Point();
        q.add(new Point(Y-1,X-1));
        while(q.isEmpty()==false) {
            size = q.size();
            cnt++;
            for(int i=0;i<size;i++)
            {
                p = q.peek();
                x = p.y;
                y = p.x;
                q.remove();
                if(visited[y][x]==true)
                    continue;
                maps[y][x] = 0;
                visited[y][x] = true;
                if(x==0 && y==0) {
                    return cnt;
                }
                if(x-1>-1 && maps[y][x-1]==1) { //왼쪽 한칸
                    q.add(new Point(y,x-1));
                }
                if(x+1<X && maps[y][x+1]==1) { //오른쪽 한칸
                    q.add(new Point(y,x+1));
                }
                if(y-1>-1 && maps[y-1][x]==1) { //위쪽 한칸
                    q.add(new Point(y-1,x));
                }
                if(y+1<Y && maps[y+1][x]==1) { //아래쪽 한칸
                    q.add(new Point(y+1,x));
                }
            }
        }
        return -1;
    }
```

## 알게된 점 및 아쉬운 점

 이전까지만 해도 일단 머리 박고 문제 풀기 시작했는데 코테를 잘보는 동기말로는 이미 수학처럼 문제 유형과 거기에 대한 모범답안은 정해졌있다고 하며, 이 문제도 그런류 중 하나라는 것이다. 좀 더 그런 부분들을 인지하고 풀면 빠르게 풀 수 있을 것 같다
