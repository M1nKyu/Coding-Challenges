---
created: 2025-03-28 13:59
title: 9663번 (N-Queen)
number: 9663
category:
  - 백트래킹
source-url:
  - https://www.acmicpc.net/problem/9663
acc-rate: 49%
solved: false
noteCompleted: true
tags:
  - "#algorithm-problem"
  - baekjoon
last_modified: 2025-03-28T14:00:00
---
### 📁 내 풀이 (X)
#### 📝 설명
- 각 좌표를 방문 -> 금지 영역 표시 -> 방문해제 -> 금지영역 표시 해제
	- 위 과정으로 재귀를 통해 구하고자 했다.
#### 📝 코드 (X)
```java
public class B9663 {

    static int N;
    static boolean visited[][];
    static int count = 0;

    // 방향:           상, 우상, 우, 우하, 하, 좌하, 좌, 좌상 (8방향)
    static int dx[] = {0,   1,    1,   1,   0,  -1,  -1,  -1};
    static int dy[] = {1,   1,    0,  -1,  -1,  -1,   0,   1};

    public static void main(String[] args) throws IOException{

        Scanner sc = new Scanner(System.in);
        N = sc.nextInt();

        visited = new boolean[N][N];

        dfs(0);
        System.out.println(count);
    }

    static void dfs(int depth){
        if(depth == N){
            count++;
            return;
        }

        for(int i = 0; i < N; i++){
            for(int j = 0; j < N; j++){
                if(!visited[i][j]){
                    visited[i][j] = true; block(i,j);
                    dfs(depth+1);
                    visited[i][j] = false; unblock(i,j);
                }
            }
        }
    }

    static void block(int i, int j){
        for(int k = 0; k < 8; k++){

            int currX = i;
            int currY = j;

            while(true){
                currX += dx[k];
                currY += dy[k];

                if(currX < 0 || currY < 0 || currX >= N || currY >= N){
                    break;
                }
                visited[currX][currY] = true;
            }
        }
    }

    static void unblock(int i, int j){
        for(int k = 0; k < 8; k++){

            int currX = i;
            int currY = j;

            while(true){
                currX += dx[k];
                currY += dy[k];

                if(currX < 0 || currY < 0 || currX >= N || currY >= N){
                    break;
                }
                visited[currX][currY] = false;
            }
        }
    }
}
```
---
### ✅ 정답 코드 및 설명
#### 📝 설명
- `arr[N]`을 각 열에서 몇 행인지를 나타내는 배열이라고 생각한다.
	- **2차원 배열을 사용하지 않고 체스판을 표현할 수 있다.**
	- 첫 번째 열부터 마지막 열까지, 어느 행에 퀸을 둘 지 결정해나간다.
#### 📝 코드 (6328 ms)
- **대각선 조건**: 두 퀸이 대각선이 위치하려면, **열 차이와 행 차이가 같아야** 한다. (열과 행의 변화량이 동일하다)
    - `if(Math.abs(depth - i) == Math.abs(arr[depth] - arr[i]))`일 때.
```java
public class B9663 {

    static int N;
    static int arr[];
    static int count = 0;

    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        N = Integer.parseInt(br.readLine());

        arr = new int[N];

        dfs(0);
        System.out.println(count);
    }

    static void dfs(int depth){
        if(depth == N){
            count++;
            return;
        }
		
		// 📌 depth는 각 열이라고 생각하면 된다. (0열부터 N-1열까지 퀸을 배치할 행을 찾아간다.)
		// 📌 depth열에서 각 i행에 대해 놓을 수 있는지 탐색하여 배치한다.
		// 📌 완료된 열(depth)은 더 이상 보지 않으므로 열 검사는 하지않는다.
        for(int i = 0; i < N; i++){ // i는 행 번호를 의미한다.
            arr[depth] = i;

			// 📌 배치가 가능한 경우 -> 배치하고 다음 열 탐색
            if(isPossible(depth)){
                dfs(depth+1);
            }
        }
    }

    static boolean isPossible(int depth){
	    // 현재 보고있는 열 이전의 열에 대해서만 비교한다. (depth 순차적으로 배치 -> 다음 열은 아직 배치하지 않았음 -> 이전만 보면됨)
        for(int i = 0; i < depth; i++){
			
			// i열에 배치된 퀸의 행과 같은 행인 경우 -> 배치할 수 없다.
            if(arr[i] == arr[depth]){
                return false;
            }
            // 📌 i열에 배치된 퀸과 대각선에 위치한 경우 -> 배치할 수 없다.
            else if(Math.abs(depth - i) == Math.abs(arr[depth] - arr[i])){
                return false;
            }
        }
        return true;
    }
}
```
#### 🔍 참고 자료
- [01](https://st-lab.tistory.com/118)
- [02](https://stdio-han.tistory.com/92)
---
### ✨ 더 좋은 방법 (Chap GPT)
- 비트마스킹 사용
#### 📝 코드
- **비트 연산**:
	- `|=`: 특정 위치를 사용 중으로 표시.
	- `&=`와 `~`: 특정 위치를 사용 해제.
```java
static int column = 0;  // 현재 열에 퀸이 배치되었는지 여부를 나타내는 비트마스크
static int diag1 = 0;   // 왼쪽 아래 ↘ 오른쪽 위 대각선을 나타내는 비트마스크
static int diag2 = 0;   // 왼쪽 위 ↖ 오른쪽 아래 대각선을 나타내는 비트마스크

static void dfs(int depth) {
    // 모든 열(depth)에 퀸을 배치한 경우 -> 유효한 배치로 카운트 증가
    if (depth == N) {
        count++;
        return;
    }

    // 현재 열(depth)에서 모든 행(i)을 탐색
    for (int i = 0; i < N; i++) {
        // 현재 행(i)에 대한 비트마스크 계산
        int colMask = 1 << i;                // i번째 열에 퀸을 배치할 수 있는지 확인
        int diag1Mask = 1 << (depth + i);    // ↘ 대각선에 퀸을 배치할 수 있는지 확인
        int diag2Mask = 1 << (depth - i + (N - 1)); // ↖ 대각선에 퀸을 배치할 수 있는지 확인

        // 현재 행(i)에 퀸을 배치할 수 없는 경우 (열, ↘ 대각선, ↖ 대각선 중 하나가 이미 사용 중)
        if ((column & colMask) != 0 || (diag1 & diag1Mask) != 0 || (diag2 & diag2Mask) != 0) {
            continue; // 다음 행으로 이동
        }

        // 현재 행(i)에 퀸을 배치 (비트마스크 갱신)
        column |= colMask;   // i번째 열 사용 표시
        diag1 |= diag1Mask;  // ↘ 대각선 사용 표시
        diag2 |= diag2Mask;  // ↖ 대각선 사용 표시

        // 다음 열(depth + 1)로 이동
        dfs(depth + 1);

        // 백트래킹: 현재 행(i)에 대한 퀸 배치 해제 (비트마스크 복구)
        column &= ~colMask;   // i번째 열 사용 해제
        diag1 &= ~diag1Mask;  // ↘ 대각선 사용 해제
        diag2 &= ~diag2Mask;  // ↖ 대각선 사용 해제
    }
}
```
