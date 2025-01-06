---
created: 2024-07-08 16:30
title: DFS와 BFS
number: 1260
category:
  - 그래프 탐색 알고리즘
  - DFS, BFS
source-url:
  - https://www.acmicpc.net/problem/1260
solved: false
noteCompleted: true
tags:
  - "#algorithm-problem"
  - baekjoon
---
## ⭐ 아이디어 노트 
- 아이디어 1
	- 그래프 클래스와 노드, 엣지 클래스를 생성하여 처리한다.
- 아이디어 2 ^d8bbb5
	- 2차원 배열을 생성하여 처리한다.
		- 하나의 노드 당 연결할 수 있는 엣지의 최대 수는 총 노드의 개수(N) - 1\
			- N만큼 노드 행을 만들고, (N-1)만큼의 엣지 열을 만듦
		- 일반 배열로 생성할 때 문제점 -> 연결경로를 추가하기 번거로움 -> ArrayList로 정의함 (add 사용을 위해)
			- `static ArrayList<ArrayList<Integer>> graph = new ArrayList<>();`
		- 😱 *그래프 탐색을 거친 노드를 어떻게 표시할 것인지?*
			- 연결된 노드에 해당하는 인덱스의 번호를 -1 / 0 / 1로 표현한다. 배열 값은 -1로 초기화.
				- -1: 연결된 엣지 없음
				-  0: 이미 탐색을 마친 노드
				- 1 : 탐색을 하지 않은 노드  
					- *이 방법 문제점: 탐색한 노드에 대해서 행마다 0으로 바꿔야 함*
			- 방문한 노드를 표시하기 위한 배열 `visitied` 사용
## ~~💯 (해결 성공 시) 나의 작성 코드~~ 
```

```
#### 해결 방법

## 💣 (해결 실패 시) 실패 코드
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.Scanner;

public class B1260 {

    static int node;
    static int edge;
    static int startNode;
    static ArrayList<ArrayList<Integer>> graph = new ArrayList<>();
    static boolean visited[];

    static boolean isCompleted(){
        for(int i = 0; i < visited.length; i++){
            if(!visited[i]){
                return false;
            }
        }
        return true;
    }

    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);

        // 
        node = sc.nextInt();
        edge = sc.nextInt();
        startNode = sc.nextInt();
        visited = new boolean[node];
        visited[startNode-1] = false;


        for(int i = 0; i < edge; i++) {
            int a = sc.nextInt();
            int b = sc.nextInt();

            graph.get(a - 1).add(b - 1);
            graph.get(b - 1).add(a - 1);
        }

        System.out.println("graph Size(root): " + graph.size());
        System.out.println("graph Size(i): " + graph.get(0).size());
        System.out.println("graph Size(i): " + graph.get(1).size());
        System.out.println("graph Size(i): " + graph.get(2).size());
        dfs();
    }

    public static void dfs(){
        System.out.println(startNode + " ");

        int curNode = startNode - 1;

        while(true){
            // 모든 노드의 탐색이 완료된 상태이면 종료
            if(isCompleted()){
                return;
            }

            int num = 1001;
            for(int i = 0; i < graph.get(curNode).size(); i++){
                if(!visited[i] && graph.get(curNode).get(i) < num){
                    num = graph.get(curNode).get(i);
                    curNode = num;
                }
            }
            System.out.println(curNode+1 + " ");
            visited[curNode] = false;

            if(num == 1001) return;
        }
    }

    public static void bfs(){

    }


}

```
#### 실패 원인, 어려움
- 순수 반복문을 통해 구현하려 했지만, 
	- 코드 복잡성 증가
	- 이는 모든 노드를 반복문으로 탐색하며, 방문한 노드를 추적하는 방식입니다. 하지만 이는 비효율적
	- 반복문으로만 구현하면, DFS의 경우 올바른 순서를 유지하기 어렵고, BFS의 경우 큐의 기능을 반복문으로 대체하기 매우 여려움

## 💡 문제 해결 로직 
#### 설명 ([원문 링크](https://infodon.tistory.com/96))
###### 1. 노드, 엣지의 표현
- 설명은 노드와 간선의 표현을 인접행렬로 표현함.
> *위의 아이디어 노트([[1260번 (DFS와 BFS)#^d8bbb5]])에서 표현한 방법이 인접 행렬이였음* 

- 입력이 아래와 같을 때
	```
	4 5 1
	1 2
	1 3
	1 4
	2 4
	3 4
	```

|     |   1   |   2   |   3   |   4   |
| :-: | :---: | :---: | :---: | :---: |
|  1  |       | **1** | **1** | **1** |
|  2  | **1** |       |       | **1** |
|  3  | **1** |       |       | **1** |
|  4  | **1** | **1** | **1** |       |
- 인접 행렬은 위와 같이 표현됨
###### DFS 구현
- DFS는 **재귀**를 활용
```java
public static void dfs(int start){
	visited[start] = true;
	sb.append(start + " ");

	for(int i = 0; i <= node; i++){
		if(graph.get(start).get(i) == 1 && visited[i] != true){
			dfs(i);
		}
	}
}
```

###### BFS 구현
- BFS는 재귀가 아닌, **큐를 사용**하여 구현
	- 시작 노드부터 큐에 넣음 -> 인접 행렬을 탐색하면서 현재 노드와 연결된 탐색하지 않은 노드를 순차적으로 큐에 넣음
```java
public static void bfs(int start){
	queue.add(start);
	visited[start] = true;

	while(!queue.isEmpty()){
		start = queue.poll();
		sb.append(start + " ");

		for(int i = 1; i <= node; i++){
			if(graph.get(start).get(i) == 1 && !visited[i]){
				queue.add(i);
				visited[i] = true;
			}
		}
	}
}
```

#### 솔루션 코드

#### 사용된 알고리즘 원리
- 🗒️[[DFS,BFS]]
- 

#### Java 문법
- [[컬렉션 프레임워크 - Queue]]

## 🔧 코드 재작성 노트 (오답 노트)
#### 재작성 코드
```java
import java.util.*;

public class B1260 {

    static int node;
    static int edge;
    static int startNode;

    static ArrayList<ArrayList<Integer>> graph = new ArrayList<>(); // dfs를 위한 배열
    static Queue<Integer> queue = new LinkedList<>(); // bfs를 위한 배열

    static boolean visited[];

    static StringBuffer sb = new StringBuffer(); // StringBuffer 사용

    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);

        node = sc.nextInt();
        edge = sc.nextInt();
        startNode = sc.nextInt();
        visited = new boolean[node + 1];

        for(int i = 0; i <= node; i++){
            graph.add(new ArrayList<>());

            for(int j = 0; j <= node; j++){
                graph.get(i).add(0);
            }
        }
        for(int i = 0; i < edge; i++) {
            int a = sc.nextInt();
            int b = sc.nextInt();

            graph.get(a).set(b, 1);
            graph.get(b).set(a, 1);
        }

        dfs(startNode);
        sb.append("\n");

        visited = new boolean[node + 1];
        bfs(startNode);

        System.out.println(sb);

    }

    public static void dfs(int start){
        visited[start] = true;
        sb.append(start + " ");

        for(int i = 0; i <= node; i++){
            if(graph.get(start).get(i) == 1 && visited[i] != true){
                dfs(i);
            }
        }
    }

    public static void bfs(int start){
        queue.add(start);
        visited[start] = true;

        while(!queue.isEmpty()){
            start = queue.poll();
            sb.append(start + " ");

            for(int i = 1; i <= node; i++){
                if(graph.get(start).get(i) == 1 && !visited[i]){
                    queue.add(i);
                    visited[i] = true;
                }
            }
        }
    }
}
```
#### 개선 및 메모 
- 설명의 원문에서는 일반 배열을 사용했지만,
	- 컬렉션 프레임워크의 ArrayList를 사용하여 인접행렬 구현

## 연관 문제




