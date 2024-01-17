# TopCoder 필요내용 정리

## DSF필요한 경우
- 모든 결로를 탐색하고 결과를를확인해야 하는 경우
- 문자열열ㄷ드응ㄹㄹ 탐색할 때 "사전순으로 앞에 오는것" 처럼 앞에서부터 검색해서 찾는것이 빠를경우
``` java
int dfs(int now){
    if(현재사태 now가 끝나는 조건) return 현재 상태 now값;
    int ret = -1; 
    for(int i = 0; i< 다음 상태 개수; i++){
        int next = i번째 다음 상태;
        if(next가 조건으 ㄹ만족하는 경우) ret = max(dfs(next), ret);
    }
    return ret;

}
```


## BFS 필요한 경우
- 시작 지점에서 가장 가까운 것으 ㄹ구하고 싶은 경우
- 탐색색범위 자체는 넉ㄹㅂ지만 어늦어도 근처에 구하고 싶은 해가가존재하는것을 알고 있는 경우
- 탐색범위가 굉장히 넓으며며깊이 우선 ㅌㅁ마색을 사용할 때는 스택이 대략으로 사용되는경우

```java
Queue<T> q = new LinkedList<T>();
q.add(초기상태);
while(!q.isEmpty()){
    T now = q.poll();
    //현재 상태 처리
    for(int i = 0 ;i< 다음상태 개수; i++){
        T next = i번째 다음 상태 
        if(next를 방문했었는지 체크){
            q.add(next);
        }
    }
}
```
