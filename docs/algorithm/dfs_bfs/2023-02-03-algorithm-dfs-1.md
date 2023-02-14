---
layout: default
title: 타겟 넘버(DFS 문제)
parent: 깊이/너비 우선 탐색(DFS/BFS) 알고리즘
grand_parent: Algorithm
permalink: /algorithm/dfs_bfs/dfs-algo-1
nav_order: 99
---

## 문제설명
 ROR 게임은 두 팀으로 나누어서 진행하며, 상대 팀 진영을 먼저 파괴하면 이기는 게임입니다. 따라서, 각 팀은 상대 팀 진영에 최대한 빨리 도착하는 것이 유리합니다. 지금부터 당신은 한 팀의 팀원이 되어 게임을 진행하려고 합니다. 

 다음은 5 x 5 크기의 맵에, 당신의 캐릭터가 (행: 1, 열: 1) 위치에 있고, 상대 팀 진영은 (행: 5, 열: 5) 위치에 있는 경우의 예시입니다.

 * bfs_algo_1_01.png
 위 그림에서 검은색 부분은 벽으로 막혀있어 갈 수 없는 길이며, 흰색 부분은 갈 수 있는 길입니다. 캐릭터가 움직일 때는 동, 서, 남, 북 방향으로 한 칸씩 이동하며, 게임 맵을 벗어난 길은 갈 수 없습니다.

 아래 예시는 캐릭터가 상대 팀 진영으로 가는 두 가지 방법을 나타내고 있습니다.
 * 첫 번째 방법은 11개의 칸을 지나서 상대 팀 진영에 도착했습니다.

## 제한사항
 - 마라톤 경기에 참여한 선수의 수는 1명 이상 100,000명 이하입니다.
 - completion의 길이는 participant의 길이보다 1 작습니다.
 - 참가자의 이름은 1개 이상 20개 이하의 알파벳 소문자로 이루어져 있습니다.
 - 참가자 중에는 동명이인이 있을 수 있습니다.


## 입출력 예
예제 #1 leo는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.
예제 #2 vinko는 참여자 명단에는 있지만, 완주자 명단에는 없기 때문에 완주하지 못했습니다.
예제 #3 mislav는 참여자 명단에는 두 명이 있지만, 완주자 명단에는 한 명밖에 없기 때문에 한명은 완주하지 못했습니다.

participant	                            completion	                      return
[leo, kiki, eden]                      	[eden, kiki]	                    leo
[marina, josipa, nikola, vinko, filipa]	[josipa, filipa, marina, nikola]	vinko

## 본인답안

```java
class Solution {
    public String solution(String[] participant, String[] completion) {
        for(int i = 0; i < participant.length; i++){
            boolean finish = true;
            
            for(int j = 0; j < completion.length; j++){
                if(completion[j].equals(participant[i])){
                    completion[j] = "";
                    finish = false;
                    break;
                }
            }
            
            if(finish){
                return participant[i];
            }
        }      
        return "문제에 맞지 않는 현상이 발견 되었습니다.";
    }
}
```

## 알게된 점 및 아쉬운 점

 - 자꾸 String배열에 length함수가 아닌 size함수를 사용한다. 베열은 length, 리스트가 size()!
 - equals() 만족하면 배열에서 제거하고 팠지만 찾지 못해서 ""값으로 대체. 개인적으로 위험하다고 판단되
 - 푸는 것만 급급해 해쉬를 사용할 생각을 못했네요.

## 점수가 높았던 답안

```java
import java.util.HashMap;

class Solution {
    public String solution(String[] participant, String[] completion) {
        String answer = "";
        HashMap<String, Integer> hm = new HashMap<>();
        for (String player : participant) hm.put(player, hm.getOrDefault(player, 0) + 1);
        for (String player : completion) hm.put(player, hm.get(player) - 1);

        for (String key : hm.keySet()) {
            if (hm.get(key) != 0){
                answer = key;
            }
        }
        return answer;
    }
}
```

