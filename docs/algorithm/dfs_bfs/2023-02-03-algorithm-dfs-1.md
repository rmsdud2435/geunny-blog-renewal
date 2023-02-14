---
layout: default
title: 타겟 넘버(DFS 문제)
parent: 깊이/너비 우선 탐색(DFS/BFS) 알고리즘
grand_parent: Algorithm
permalink: /algorithm/dfs_bfs/dfs-algo-1
nav_order: 99
---

## 문제설명
 n개의 음이 아닌 정수들이 있습니다. 이 정수들을 순서를 바꾸지 않고 적절히 더하거나 빼서 타겟 넘버를 만들려고 합니다. 예를 들어 [1, 1, 1, 1, 1]로 숫자 3을 만들려면 다음 다섯 방법을 쓸 수 있습니다.
 - -1+1+1+1+1 = 3
 - +1-1+1+1+1 = 3
 - +1+1-1+1+1 = 3
 - +1+1+1-1+1 = 3
 - +1+1+1+1-1 = 3
 사용할 수 있는 숫자가 담긴 배열 numbers, 타겟 넘버 target이 매개변수로 주어질 때, 숫자를 적절히 더하고 빼서 타겟 넘버를 만드는 방법의 수를 return 하도록 solution 함수를 작성해주세요.


## 제한사항
 - 주어지는 숫자의 개수는 2개 이상 20개 이하입니다.
 - 각 숫자는 1 이상 50 이하인 자연수입니다.
 - 타겟 넘버는 1 이상 1000 이하인 자연수입니다.


## 입출력 예
```java
//numbers			target	return
//[1, 1, 1, 1, 1]	3		5
//[4, 1, 2, 1]		4		2
```

## 입출력 예 설명

### 입출력 예 #1

문제 예시와 같습니다.

### 입출력 예 #2
- +4+1-2+1 = 4
- +4-1+2-1 = 4
총 2가지 방법이 있으므로, 2를 return 합니다.

## 출처
 - 프로그래머스: https://school.programmers.co.kr/learn/courses/30/lessons/43165


## 문제풀이

### 본인답안

```java
public class 타겟_넘버 {
    public int solution(int[] numbers, int target) {
    	int answer = 0;
        answer = getSum(numbers,target, 0, 0);
        return answer;
    }// solution
    
    public int getSum(int[] numbers, int target, int sum, int order){  
        if(order == numbers.length){
            if(sum==target){
                return 1;
            }else{
                return 0;
            }
        }else{
            int leftResult = sum - numbers[order]; 
            int rightResult = sum + numbers[order];
            return getSum(numbers, target, rightResult, order+1) + getSum(numbers, target, leftResult, order+1);
        }
    }
}
```

### 모법답안
```java
public int solution2(int[] numbers, int target) {
    int answer = 0;
    answer = dfs(numbers, 0, 0, target);
    return answer;
}

int dfs(int[] numbers, int n, int sum, int target) {
    if(n == numbers.length) {
        if(sum == target) {
            return 1;
        }
        return 0;
    }
    return dfs(numbers, n + 1, sum + numbers[n], target) + dfs(numbers, n + 1, sum - numbers[n], target);
}
```


## 알게된 점 및 아쉬운 점

 - 자꾸 String배열에 length함수가 아닌 size함수를 사용한다. 베열은 length, 리스트가 size()!
 - equals() 만족하면 배열에서 제거하고 팠지만 찾지 못해서 ""값으로 대체. 개인적으로 위험하다고 판단되
 - 푸는 것만 급급해 해쉬를 사용할 생각을 못했네요.

